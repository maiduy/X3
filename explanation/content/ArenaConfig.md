
---

# 📘 **Arena Configuration Overview**

File JSON này định nghĩa toàn bộ thông số cho hai chế độ đấu **Arena** trong game:

1. **Ranked Mode (ARENA_RANKED)** – đấu xếp hạng ELO.
2. **Casual Mode (ARENA_CASUAL)** – đấu thường, không ảnh hưởng ELO.

Mục tiêu: chuẩn hóa **matchmaking, reward, ranking, battle rules** để client, server và BI dùng chung.

---

# 🧩 **1. Cấu trúc chung của một Arena Mode**

Mỗi chế độ Arena có các thành phần:

1. `id` → định danh duy nhất (ARENA_RANKED, ARENA_CASUAL).
2. `meta` → thông tin hiển thị:

| Thuộc tính     | Ý nghĩa                         |
| -------------- | ------------------------------- |
| nameKey        | Key đa ngôn ngữ cho tên Arena   |
| descriptionKey | Key đa ngôn ngữ cho mô tả Arena |
| icon           | Biểu tượng hiển thị trên UI     |

3. `availability` → điều kiện mở khóa:

| Thuộc tính      | Ý nghĩa                                          |
| --------------- | ------------------------------------------------ |
| unlockLevel     | Level người chơi đạt được để mở                  |
| unlockCondition | Điều kiện bổ sung (ví dụ: đánh bại Boss Stage 3) |

4. `matchmaking` → luật ghép trận (chỉ cho Ranked):

| Thuộc tính        | Ý nghĩa                                       |
| ----------------- | --------------------------------------------- |
| algorithm         | ELO, PowerBased…                              |
| matchmakingRange  | Chênh lệch Elo/Power để tìm trận              |
| maxSearchTime     | Giây tìm trận tối đa                          |
| botMatchThreshold | % trường hợp dùng bot nếu không tìm được trận |

5. `seasons` (chỉ Ranked) → định nghĩa mùa giải:

* duration → số ngày mỗi season.
* resetDay → ngày bắt đầu mùa mới.
* preseasonDays → thời gian thử nghiệm trước season.
* offseasonDays → thời gian nghỉ giữa các mùa.

6. `rankingSystem` → chi tiết xếp hạng và rewards (chỉ Ranked):

* `initialRank` → Rank ban đầu khi mới unlock.
* `tiers` → các tier xếp hạng (Bronze → Grandmaster)

  * mỗi tier có divisions, eloRange, thresholds, icon, rewards weekly & seasonal.
  * `limitedSlots` → cho tier Master & Grandmaster.

7. `eloSystem` → chỉ cho Ranked:

* kFactor, placementMatches, maxGain/Loss per match.
* winStreakBonus → bonus cho chuỗi thắng.
* demotionProtection → bảo vệ tụt hạng.

8. `battleRules` → luật chiến đấu chung:

* teamSize → số người mỗi team.
* banPhase / pickPhase → bật/tắt, số lượt cấm, thời gian pick, cho phép duplicate hay không.
* turnLimit → số lượt tối đa mỗi trận.
* timeLimit → giây tối đa mỗi trận.

9. `rewards` → thưởng theo kết quả trận:

* perWin, perLoss, firstWinOfDay.
* itemId + amount.

10. `leaderboard` → bảng xếp hạng (chỉ Ranked):

* updateFrequency → giây cập nhật bảng xếp hạng.
* displayTopN → số top hiển thị.
* topRewards → reward theo rank / rankRange.

11. `noEloChange` (Casual) → True nếu trận không ảnh hưởng Elo.
12. `dailyLimit` → giới hạn số trận mỗi ngày.

---

# 🧩 **2. Ranked Mode (ARENA_RANKED)**

* Unlock: level 15 + clear Stage 3 Boss.
* Matchmaking: **ELO** ±200, max search 60s, bot threshold 90%.
* Seasons: 30 ngày, reset ngày 1, preseason 3d, offseason 2d.
* Ranking: Bronze → Grandmaster (có rewards weekly & seasonal).

  * Grandmaster giới hạn 100 slots.
* Elo System: k=32, max gain/loss, win streak bonus, demotion protection.
* Battle Rules: 5v5, ban 2/player, pick 45s, turnLimit 50, timeLimit 600s.
* Rewards: perWin 15 coin, perLoss 5 coin, firstWinOfDay 50 coin + 10 gem.
* Leaderboard: top 100, reward rank 1-3 + ranges 4-100.

---

# 🧩 **3. Casual Mode (ARENA_CASUAL)**

* Unlock: level 10, no condition.
* Matchmaking: PowerBased ±500, max search 30s, bot threshold 60%.
* Battle Rules: 5v5, no banPhase, pickPhase 30s, allowDuplicates true, turnLimit 50, timeLimit 600s.
* Rewards: perWin 500 gold, perLoss 200 gold.
* ELO không thay đổi (`noEloChange: true`).
* Không giới hạn trận hàng ngày.

---

# 🏗 **4. Tóm tắt giá trị cấu hình**

* Chuẩn hóa **Arena Mode** cho server, client, UI.
* Dễ mở rộng thêm Tier, rewards, battle rules.
* Tách biệt Ranked vs Casual.
* Hỗ trợ multi-season, leaderboard, reward automation.
* Cho phép balance Elo, match quality, reward fairness.

---