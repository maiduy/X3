Dựa trên các tệp cấu hình chi tiết bạn cung cấp (`Characters.json`, `GrowthCurves.json`, `Levels.json`), đây là thiết kế chi tiết cho hệ thống NATS JetStream để xử lý sự kiện **Character Level Up**.

Hệ thống này đảm bảo tính nhất quán của dữ liệu (Data Consistency) khi tính toán chỉ số nhân vật dựa trên các công thức phức tạp trong `GrowthCurves`.

### 1\. Cấu hình JetStream (Stream Configuration)

Chúng ta sẽ tạo một Stream bền vững để lưu trữ lịch sử phát triển của người chơi.

  * **Stream Name:** `GAME_PROGRESSION`
  * **Subjects:** `game.progression.>`
  * **Storage:** `File` (Bền vững)
  * **Retention:** `Limits` (Hoặc `WorkQueue` nếu muốn xử lý xong xóa, nhưng `Limits` tốt hơn cho Replay/Debug).

-----

### 2\. Định nghĩa Sự kiện Trigger (Payload)

Khi người chơi dùng vật phẩm EXP hoặc hoàn thành `STAGE_1_1` (nhận 150 exp), Character Service sẽ phát hiện nhân vật đủ EXP lên cấp và bắn sự kiện này.

**Subject:** `game.progression.character.levelup`

**Payload (JSON):**

```json
{
  "event_id": "evt_uuid_8888",
  "timestamp": "2025-11-24T10:00:00Z",
  "user_id": "user_123",
  "character_id": "CHAR_FIRE_DRAGON",
  "growth_curve_id": "CURVE_WARRIOR_S_TIER", 
  "old_level": 19,
  "new_level": 20,
  "reason": "STAGE_CLEAR", // Hoặc "ITEM_USE"
  "meta": {
    "stage_id": "STAGE_1_BOSS" // Nếu up level do đi ải
  }
}
```

*Lưu ý: Chúng ta gửi kèm `growth_curve_id` (lấy từ `Characters.json`) để các consumer không phải query lại DB để tìm curve ID.*

-----

### 3\. Thiết kế các Microservice Consumers

Dưới đây là chi tiết logic xử lý cho từng Service dựa trên dữ liệu file bạn gửi.

#### **A. Player Stat Calculator (Service Tính Toán Chỉ Số)**

Đây là consumer quan trọng nhất. Nhiệm vụ của nó là hiện thực hóa các công thức trong `GrowthCurves.json`.

  * **Subscribe Subject:** `game.progression.character.levelup`
  * **Queue Group:** `service-stats-calc`
  * **Logic xử lý:**
    1.  **Nhận Event:** `CHAR_FIRE_DRAGON` lên Level 20. Curve: `CURVE_WARRIOR_S_TIER`.
    2.  **Load Base Stats (`Characters.json`):**
          * `hp`: 850, `atk`: 120, `def`: 65.
    3.  **Load Growth Formula (`GrowthCurves.json`):**
          * Tìm `CURVE_WARRIOR_S_TIER`.
          * Tại Level 20, `breakpoints` cho HP có multiplier là `2.52`.
    4.  **Tính toán (Execution):**
          * $NewHP = 850 \times 2.52 = 2142$
          * $NewAtk = 120 \times 2.14 = 256.8$ (Giả sử breakpoint ATK là 2.14)
    5.  **Persistence:** Cập nhật stats mới vào Database.
    6.  **Publish Result:** Bắn sự kiện `stats.updated` để Client UI cập nhật.

**Output Payload (Bắn ra sau khi tính xong):**

```json
// Subject: game.player.stats.updated
{
  "user_id": "user_123",
  "character_id": "CHAR_FIRE_DRAGON",
  "level": 20,
  "stats": {
    "hp": 2142,
    "atk": 257,
    "def": 151,
    "spd": 95 // Spd multiplier là 1.0
  },
  "combat_power": 5400 // Công thức tổng hợp lực chiến
}
```

#### **B. Skill Service (Service Kỹ Năng)**

Kiểm tra xem level mới có mở khóa skill slot nào trong `skillSet` không.

  * **Subscribe Subject:** `game.progression.character.levelup`
  * **Queue Group:** `service-skills`
  * **Logic xử lý:**
      * Load `Characters.json` -\> `skillSet`.
      * Trong game design (thường nằm ở file config khác hoặc logic code), giả sử Level 20 mở khóa `skill2`.
      * Mapping: `skill2` của `CHAR_FIRE_DRAGON` là `SKILL_DRAGON_RAGE`.
      * Action: Active skill `SKILL_DRAGON_RAGE` trong DB skill của user.

#### **C. Mission/LiveOps Service (Nhiệm vụ & Sự kiện)**

Xử lý các nhiệm vụ Battle Pass hoặc Tân thủ.

  * **Subscribe Subject:** `game.progression.character.levelup`
  * **Queue Group:** `service-missions`
  * **Logic xử lý:**
      * User có nhiệm vụ: "Nâng 1 tướng hệ Fire lên Level 20".
      * Check payload: `new_level` == 20? `character_id` ("CHAR\_FIRE\_DRAGON") có `meta.element` == "Fire"? (Cần cache `Characters.json` để check element).
      * Action: Đánh dấu nhiệm vụ hoàn thành -\> Gửi quà qua Mail Service.

#### **D. Economy Service (Tiêu thụ tài nguyên)**

Nếu việc up level tốn vàng (trường hợp user dùng bình EXP + Vàng để up level nhanh).

  * **Subscribe Subject:** `game.progression.character.levelup`
  * **Logic xử lý:**
      * Nếu `reason` == "MANUAL\_UPGRADE".
      * Load `GrowthCurves.json` -\> `exponentialCurves` -\> `CURVE_ENHANCEMENT_COST` (hoặc tạo curve mới cho XP cost).
      * Tính cost: `floor(100 * pow(1.1, 19))` (ví dụ).
      * Action: Trừ vàng của user (Validate transaction). *Lưu ý: Thường bước này làm Synchronous (API) trước khi bắn event LevelUp để tránh hack, nhưng log trừ tiền có thể đẩy qua stream.*

-----

### 4\. Tại sao thiết kế này phù hợp với file JSON của bạn?

1.  **Tận dụng `GrowthCurves.json`:** Logic tính toán tách biệt hoàn toàn khỏi code cứng. Nếu bạn muốn nerf tướng Warrior, bạn chỉ cần sửa `CURVE_WARRIOR_S_TIER` trong JSON, restart service, và toàn bộ hệ thống tính toán sẽ cập nhật theo mà không cần sửa code từng nhân vật.
2.  **Xử lý `Levels.json`:** Khi user clear `STAGE_1_BOSS`, phần thưởng `exp` (500) được cộng vào. Service sẽ tính: `CurrentExp + 500 >= RequiredExp` (từ `CURVE_EXP_REQUIRED`)? Nếu có -\> Bắn event LevelUp.
3.  **Decoupling:** Service tính Stats không cần biết user đang chơi màn nào, nó chỉ quan tâm đến Level và Curve ID.