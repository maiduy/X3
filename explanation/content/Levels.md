
---

# 📘 **Campaign & Dungeon Configuration Overview**

File JSON này định nghĩa **nội dung cốt truyện, stages, boss, và dungeon** trong game. Nó chuẩn hóa **progression, rewards, difficulty, unlock conditions** để backend, client và BI sử dụng chung.

---

# 🧩 **1. Campaigns**

Mỗi campaign bao gồm:

1. `id` → định danh duy nhất (ví dụ: CAMPAIGN_MAIN_STORY).
2. `meta` → thông tin hiển thị và điều kiện mở khóa:

| Thuộc tính     | Ý nghĩa                            |
| -------------- | ---------------------------------- |
| nameKey        | Key đa ngôn ngữ cho tên campaign   |
| descriptionKey | Key đa ngôn ngữ cho mô tả campaign |
| icon           | Biểu tượng UI                      |
| chapters       | Số lượng chương                    |
| unlockLevel    | Level mở khóa campaign             |

3. `chapters` → từng chương trong campaign:

* `id` → ID chương.
* `meta` → thông tin chương (chapterNumber, storyUnlock…)
* `stages` → các stage trong chương:

---

## 🔹 **Stage Structure**

Mỗi stage định nghĩa:

1. `id` → Stage ID (STAGE_1_1, STAGE_1_BOSS…).
2. `meta` → thông tin stage:

| Thuộc tính       | Ý nghĩa                   |
| ---------------- | ------------------------- |
| nameKey          | Key đa ngôn ngữ cho stage |
| stageNumber      | Số thứ tự stage           |
| difficulty       | Normal / Hard / Boss      |
| recommendedPower | Sức mạnh khuyến nghị      |
| isBossStage      | True nếu là boss          |

3. `requirements` → điều kiện unlock & năng lượng:

| Thuộc tính      | Ý nghĩa                     |
| --------------- | --------------------------- |
| unlockCondition | Stage trước phải hoàn thành |
| minLevel        | Level tối thiểu             |
| energyCost      | Năng lượng tiêu hao         |

4. `mapConfig` → map & môi trường:

| Thuộc tính    | Ý nghĩa                       |
| ------------- | ----------------------------- |
| mapId         | ID bản đồ                     |
| environment   | Loại môi trường               |
| weatherEffect | Hiệu ứng thời tiết (optional) |

5. `enemies` → định nghĩa waves & units:

* `waves` → mỗi wave gồm `waveNumber` & `units`.
* `units` → enemyId, level, position, isBoss, aiProfile.

6. `victoryCondition` → cách chiến thắng:

* type: ELIMINATE_ALL, KILL_BOSS…
* params: targetTag, điều kiện chi tiết.

7. `rewards` → phần thưởng stage:

* firstClear → thưởng lần đầu.
* repeat → thưởng lặp lại.
* exp → stage exp.
* playerExp → exp người chơi.

8. `drops` → bảng drop items:

* table → ID drop table.
* bonus → (optional, có thể null).
* guaranteedRare → true nếu boss drop rare item chắc chắn.

9. `stars` → điều kiện đạt sao:

* ClearStage, NoDeaths, ClearUnderXTurns, với reward itemId + amount.

---

## 🔹 **Ví dụ Stage Boss**

* `id: STAGE_1_BOSS`
* Difficulty: Boss, recommendedPower 1200
* Victory: KILL_BOSS, targetTag: "Boss"
* Reward: gold 2000, gem 50, summon scroll 5, character fragment 10
* Drops: DROP_TABLE_BOSS_CHAPTER_1, guaranteedRare: true
* Stars: ClearStage (gem 20), NoDeaths (char fragment 5), ClearUnder20Turns (rare ore 5)

---

# 🧩 **2. Dungeons**

Mỗi dungeon có:

1. `id` → DUNGEON_GOLD_VAULT, DUNGEON_EXP_TOWER…
2. `meta` → thông tin dungeon:

| Thuộc tính     | Ý nghĩa                      |
| -------------- | ---------------------------- |
| nameKey        | Tên đa ngôn ngữ              |
| descriptionKey | Mô tả                        |
| icon           | Biểu tượng                   |
| type           | Resource / Boss / Challenge… |
| availability   | Ngày mở, level unlock        |

3. `difficulties` → nhiều mức độ:

| Thuộc tính       | Ý nghĩa                   |
| ---------------- | ------------------------- |
| difficulty       | Easy / Hard / Hell        |
| recommendedPower | Power khuyến nghị         |
| energyCost       | Năng lượng tiêu hao       |
| enemyLevel       | Level kẻ địch             |
| rewards          | Guaranteed & random / exp |

4. `dailyLimit` → số lượt chơi tối đa mỗi ngày.

---

## 🔹 **Ví dụ Dungeon Resource**

* **DUNGEON_GOLD_VAULT**

  * Easy: gold 5000, energy 10, enemyLevel 15
  * Hard: gold 15000, energy 15, enemyLevel 30
  * Hell: gold 50000, energy 20, enemyLevel 50
  * Daily limit: 3

* **DUNGEON_EXP_TOWER**

  * Easy: exp potion small 10, exp 5000
  * Hard: exp potion medium 5, exp 15000
  * Daily limit: 3

---

# 🏗 **3. Tóm tắt giá trị cấu hình**

* Chuẩn hóa **Campaign Progression**: unlockCondition, map, enemies, victory, rewards.
* Tách biệt **Stage thường & Boss**.
* Hỗ trợ multi-reward: firstClear, repeat, stars.
* Dungeons: hỗ trợ multi-difficulty, resource/gold/exp rewards, daily limit.
* Dễ mở rộng thêm stage, dungeon, reward, drop table.

---