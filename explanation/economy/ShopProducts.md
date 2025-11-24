
---

# 🏪 **Shops / Store System Overview**

File JSON này định nghĩa các **shop trong game**, bao gồm:

* **IAP Store (Gem Store)**
* **Currency-based Shops (Arena, Guild)**
* **Special / Daily Deals Shops**

Mỗi shop có thể chứa **nhiều sản phẩm** (products), kèm **giá**, **nội dung**, **giới hạn**, **thời gian mở**, và **lịch làm mới**.

---

# 🧩 **1. Shop Structure**

Mỗi shop có các trường chính:

| Thuộc tính      | Ý nghĩa                                                             |
| --------------- | ------------------------------------------------------------------- |
| id              | Unique shop ID (SHOP_GEM_STORE, SHOP_ARENA…)                        |
| meta            | Metadata hiển thị: nameKey, descriptionKey, icon, type, unlockLevel |
| currency        | Nếu shop dùng currency riêng (Arena Coin, Guild Coin)               |
| products        | Danh sách item/sản phẩm có thể mua                                  |
| refreshSchedule | Lịch làm mới sản phẩm (Daily / Weekly, resetTime / resetDay)        |

---

# 🧩 **2. Product Structure**

Mỗi sản phẩm trong shop có các trường:

| Thuộc tính    | Ý nghĩa                                                                 |
| ------------- | ----------------------------------------------------------------------- |
| id            | Unique product ID                                                       |
| meta          | Metadata: nameKey, descriptionKey, icon, displayOrder, badge            |
| price         | Giá: USD (IAP) hoặc currency game (itemId + amount)                     |
| contents      | Nội dung nhận được khi mua                                              |
| bonusContents | Nội dung bonus kèm điều kiện (FirstPurchase…)                           |
| dailyRewards  | Nếu là pass (ví dụ Monthly Pass), cấu hình duration & rewards hàng ngày |
| limits        | Giới hạn mua: perDay, perWeek, perMonth, perAccount, renewalAllowed     |
| availability  | Thời gian mở shop / sản phẩm (startDate, endDate)                       |
| discount      | Tỷ lệ giảm giá (optional)                                               |

---

# 🔹 **Ví dụ Shop: Gem Store (IAP)**

* `type: IAP`, unlockLevel 1
* Products:

  * **IAP_GEM_PACK_SMALL**: $0.99 → 60 gems (+10 bonus first purchase)
  * **IAP_GEM_PACK_MEDIUM**: $4.99 → 330 gems (+50 bonus first purchase)
  * **IAP_GEM_PACK_LARGE**: $9.99 → 680 gems (+100 bonus first purchase, badge: POPULAR)
  * **IAP_MONTHLY_PASS**: $4.99 → 300 gems + daily rewards 30 days, perAccount 1, renewalAllowed true, badge: BEST_VALUE

---

# 🔹 **Ví dụ Shop: Arena Shop (Currency-based)**

* Currency: **CURRENCY_ARENA_COIN**
* unlockLevel: 15
* Products:

  * **ARENA_SUMMON_SCROLL**: 200 Arena Coins → 1 Summon Scroll, limit perWeek: 5
  * **ARENA_CHAR_FRAGMENT**: 50 Arena Coins → Fire Dragon Fragment, limit perMonth: 30
  * **ARENA_GOLD**: 10 Arena Coins → 10,000 Gold, limit perDay: 10
* Refresh: Daily, resetTime: 00:00

---

# 🔹 **Ví dụ Shop: Guild Shop (Currency-based)**

* Currency: **CURRENCY_GUILD_COIN**, unlockLevel: 20
* Products:

  * **GUILD_DRAGON_SCALE**: 500 Guild Coins → Dragon Scale, perWeek 3
  * **GUILD_LEGENDARY_ORE**: 300 Guild Coins → Legendary Ore, perWeek 5
* Refresh: Weekly, resetDay: Monday (1)

---

# 🔹 **Ví dụ Shop: Daily Deals (Special Shop)**

* Type: Special, unlockLevel 5
* Products:

  * **DAILY_GOLD_DEAL**: 50 Gems → 50,000 Gold, limit perDay 1, discount 50%, badge: DAILY
  * **DAILY_ENERGY_DEAL**: 30 Gems → 100 Energy, limit perDay 3, badge: DAILY
* Refresh: Daily, resetTime: 00:00

---

# 🏗 **3. Tổng hợp**

* Hệ thống **đa dạng shop**, hỗ trợ:

  * **IAP (Gem Store / Monthly Pass)**
  * **Currency-based Shop (Arena / Guild)**
  * **Special / Daily Deals Shop**
* Tất cả sản phẩm đều có: **giá**, **contents**, **bonus**, **limits**, **availability**, optional **discount/badge**
* Shops có **lịch refresh** (Daily/Weekly)
* Dễ mở rộng với **shop mới**, **product mới**, **currency mới**, **limits**, **bonuses**, **pass rewards**

---
