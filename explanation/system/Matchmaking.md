
---

# 🎮 **Matchmaking Configuration Overview**

File này định nghĩa cách thức **ghép trận (matchmaking)** trong game, bao gồm:

* **Ranked Matches** (xếp hạng)
* **Casual Matches** (thông thường)
* **Co-op Matches** (nhóm đồng đội)
* **Chống smurf / gian lận**
* **Fairness & Region rules**
* **Backfill system** (bù người thiếu trận)

---

## 1️⃣ **Ranked Matchmaking**

* **Algorithm:** `ELO` (xếp hạng dựa trên Elo)
* **Parameters:**

  * `matchmakingRange`: mở rộng dần khoảng chênh lệch Elo nếu không tìm được trận

    * initial: 100, expansionRate: 50, maxRange: 500, expansionInterval: 10s
  * `eloConsideration`: trọng số 0.7, rangeMultiplier 1.0
  * `powerConsideration`: trọng số 0.2, maxDifference 2000
  * `winStreakConsideration`: trọng số 0.1, bonus 10 Elo mỗi chiến thắng liên tiếp
* **Queue Priority:**

  * `waitTimeBonus`: tăng ưu tiên dựa trên thời gian chờ (0.5 điểm/s, max 100)
  * `tierProtection`: ngăn chặn cross-tier cho Master & Grandmaster
* **Timeouts:**

  * maxSearchTime: 120s, botMatchThreshold: 90s, requeueDelay: 5s
* **Bot Matching:**

  * enabled, botEloRange 50, ưu tiên real players, botDifficultyScaling true

✅ Tối ưu để **tìm trận cân bằng Elo nhưng vẫn giảm thời gian chờ**.

---

## 2️⃣ **Casual Matchmaking**

* **Algorithm:** `PowerBased` (dựa trên sức mạnh / power)
* **Parameters:**

  * `powerRange`: initial 500, expansionRate 200, maxRange 2000, expansionInterval 15s
  * `levelConsideration`: trọng số 0.3, maxDifference 10 level
* **Timeouts:** maxSearchTime 60s, botMatchThreshold 45s, requeueDelay 3s
* **Bot Matching:** enabled, botPowerRange 300, preferRealPlayers false, botDifficultyScaling true

✅ Tìm trận nhanh, ưu tiên trải nghiệm hơn là cân bằng chặt chẽ.

---

## 3️⃣ **Co-op / Party Matchmaking**

* **Algorithm:** `PartyBased`
* **Parameters:**

  * `partySize`: min 1, max 4
  * `averagePowerMatching`: true → tính trung bình sức mạnh party
  * `roleBalancing`:

    * enabled, preferredComposition: Tank 1, DPS 2, Support 1
* **Timeouts:** maxSearchTime 90s, fillWithBots true, requeueDelay 5s

✅ Đảm bảo **party cân bằng vai trò & sức mạnh**, và hỗ trợ bot khi thiếu người.

---

## 4️⃣ **Anti-Smurfing / Anti-Cheat**

* **enabled:** true
* **New Account Detection:**

  * winRateThreshold 0.8, gamesPlayed 20 → nếu vượt, hành động: AccelerateEloGain
* **Elo Boost Detection:**

  * enabled, suspiciousPatterns: RapidEloGain, PartyMMRGap
  * hành động: FlagForReview

✅ Ngăn người chơi tạo tài khoản mới mạnh nhanh (smurf) và gian lận Elo.

---

## 5️⃣ **Fairness Rules**

* **maxPartyMMRDifference:** 400
* **soloQueueProtection:**

  * enabled, separateQueue false, maxPartySize 2
* **regionLocking:**

  * enabled, preferSameRegion true, crossRegionThreshold 60s

✅ Đảm bảo **trận đấu công bằng**, hạn chế cross-region & chênh lệch sức mạnh quá lớn.

---

## 6️⃣ **Backfill System**

* **enabled:** true, allowBackfill true
* **Rewards:** bonusGold 1000, bonusExp 100

✅ Khi có người rời trận, hệ thống tự động **bù bằng bot** và thưởng để khuyến khích tham gia.

---

### 🔹 **Tóm tắt**

* **Ranked:** Cân bằng Elo + thưởng win streak + bot khi cần
* **Casual:** Nhanh, dựa trên power, bot tùy chỉnh
* **Co-op:** Cân bằng party, vai trò & sức mạnh, bot hỗ trợ
* **Anti-Smurf:** Phát hiện new account / rapid Elo gain
* **Fairness:** Giới hạn MMR party, bảo vệ solo, khóa vùng
* **Backfill:** Điền bot + thưởng bù

---
