
---

# 🎁 **Rewards & Drop System Overview**

File này định nghĩa các cơ chế thưởng và rớt vật phẩm trong game:

1. **Drop Tables** – vật phẩm rơi khi hoàn thành stage hoặc đánh boss
2. **Achievement Rewards** – thưởng theo thành tích
3. **Level Up Rewards** – thưởng theo level người chơi
4. **First Time Rewards** – thưởng lần đầu (login, stage, gacha, PvP)
5. **Streak Rewards** – thưởng theo chuỗi ngày login hoặc chuỗi thắng
6. **Random Reward Boxes** – hộp thưởng rút ngẫu nhiên

---

## 1️⃣ **Drop Tables**

### Chapter 1 Normal Drops

* `MAT_COMMON_ORE`: 30%, 1–3 cái
* `MAT_WEAPON_ORE`: 15%, 1–2 cái
* `CURRENCY_GOLD`: 100%, 50–150 vàng
* `ITEM_EXP_POTION_SMALL`: 20%, 1–2 cái

### Chapter 1 Boss Drops

* `MAT_RARE_ORE`: 50%, 1–3 cái
* `CHAR_SHADOW_ASSASSIN_FRAGMENT`: 25%, 1–2 mảnh
* `CURRENCY_GOLD`: 100%, 500–1000 vàng
* `ITEM_EQUIPMENT_RANDOM_SR`: 10%, 1 món

✅ Bao gồm **drops thường & boss**, mỗi item có **tỉ lệ rơi và số lượng min/max**.

---

## 2️⃣ **Achievement Rewards**

* **First Victory:** stage đầu tiên

  * Gem: 50, Gold: 5000
* **Stage Master:** hoàn thành 100 stage

  * Gem: 500, Summon Scroll: 10, Exclusive Title: 1
* **Gacha Addict:** 1000 lần pull gacha

  * Gem: 1000, Exclusive Avatar: 1

✅ Thưởng **trải đều theo tiến trình & hành vi chơi**, bao gồm vật phẩm tiêu chuẩn và độc quyền.

---

## 3️⃣ **Level Up Rewards**

* **Level 5:** Gold 10k, Energy 50
* **Level 10:** Gem 100, Summon Scroll 5
* **Level 20:** Gem 200, Summon Scroll 10, Frost Mage Fragment 50
* **Level 30:** Gem 500, Summon Scroll 20
* **Level 50:** Gem 1000, Summon Scroll 50, Random SSR Equipment 1

✅ Tăng dần **giá trị thưởng** theo level để khuyến khích tiến trình.

---

## 4️⃣ **First Time Rewards**

* **First Login:** Gold 50k, Gem 500, Summon Scroll 10
* **First Stage Complete:** Gold 5k, Gem 20
* **First Gacha Pull:** Gold 10k
* **First PvP Win:** Arena Coin 100, Gem 50

✅ **Khuyến khích trải nghiệm game lần đầu**, tăng retention.

---

## 5️⃣ **Streak Rewards**

### Login Streak

* Day 3: Gem 50
* Day 7: Gem 200 + Summon Scroll 5
* Day 14: Gem 500 + Summon Scroll 10 + Exclusive Avatar Loyal 1
* Day 30: Gem 1000 + Summon Scroll 30 + Random SSR Equipment 1

### Win Streak (Arena)

* Bonus multiplier: 1.1 → max 2.0

✅ **Thưởng liên tục** để giữ người chơi quay lại hằng ngày.

---

## 6️⃣ **Random Reward Boxes**

### Bronze Box (Common)

* Gold: weight 50, 1k–5k
* MAT_COMMON_ORE: weight 30, 3–10
* EXP Potion Small: weight 20, 2–5
* **Guaranteed Count:** 3

### Gold Box (Rare)

* Gem: weight 20, 50–200
* Summon Scroll: weight 30, 1–5
* MAT_RARE_ORE: weight 30, 3–10
* Random SR Equipment: weight 20, 1
* **Guaranteed Count:** 5

✅ Hệ thống **rút ngẫu nhiên theo trọng số (weight)**, đảm bảo **một số vật phẩm chắc chắn rơi**.

---

### 🔹 **Tóm tắt**

* **Drop Tables:** item rơi stage/boss với tỉ lệ và số lượng rõ ràng
* **Achievements:** thưởng khi hoàn thành milestone game
* **Level Up:** thưởng theo cấp độ, tăng dần
* **First Time Rewards:** khuyến khích người chơi mới
* **Streak Rewards:** giữ retention và khuyến khích chơi liên tục
* **Random Boxes:** thưởng theo trọng số, kèm guaranteed items

---
