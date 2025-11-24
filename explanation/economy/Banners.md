
---

# 📘 **Banner / Gacha System Overview**

File JSON này định nghĩa các **banner (gacha)** trong game, bao gồm:

* **Permanent / Limited / Beginner banners**
* **Pity system** (soft/hard pity, carry over, guaranteed pickup)
* **Rates & Pools**
* **Bonuses & Discounts**
* **Drop rate display** & **Wish system**

---

# 🧩 **1. Banner Structure**

Mỗi banner gồm các trường:

| Thuộc tính | Ý nghĩa                                                                                         |
| ---------- | ----------------------------------------------------------------------------------------------- |
| id         | Unique banner ID (BANNER_STANDARD, BANNER_FIRE_DRAGON_V1…)                                      |
| meta       | Metadata hiển thị: nameKey, descriptionKey, icon, type, isPermanent, featuredCharacter (nếu có) |
| duration   | Start & end datetime; optional expiresAfterDays / expiresAfterPulls cho banner Beginner/limited |
| cost       | Giá pull single & multi (itemId + amount)                                                       |
| pity       | Cấu hình soft/hard pity, resetOnSSR, carryOver, guaranteedPickupOn                              |
| rates      | Xác suất SSR / SR / R                                                                           |
| pool       | Characters/units trong banner, weight & pickup flag                                             |
| pickup     | Cấu hình pickup banner (nếu có)                                                                 |
| guarantees | Multi-pull, first ten pull, pickup guarantee…                                                   |
| bonuses    | First pull discount, daily discount, generic discount                                           |
| maxPulls   | Giới hạn số lần pull (optional)                                                                 |

---

## 🔹 **Ví dụ Banner Permanent**

* `id: BANNER_STANDARD`
* `type: Permanent`
* `pity`: soft 70, hard 90, resetOnSSR true, carryOver true
* `rates`: SSR 2%, SR 18%, R 80%
* Pool SSR: Fire Dragon, Holy Knight (weight 1.0)
* Guarantee: multiPullGuarantee (10-pull ≥ SR)
* Bonus: firstPull 50% discount

---

## 🔹 **Ví dụ Banner Limited**

* `id: BANNER_FIRE_DRAGON_V1`
* `type: Limited`, featuredCharacter: CHAR_FIRE_DRAGON
* Duration: 2025-11-01 → 2025-11-15
* Pity: soft 50, hard 90, guaranteedPickupOn 2, carryOver false
* Rates: SSR 2%, SR 18%, R 80%
* Pool SSR: Fire Dragon 50% pickup, Holy Knight 50%
* Guarantees: multiPull & pickupGuarantee (next SSR guaranteed pickup nếu lose 50/50)
* Bonuses: dailyDiscount 20%

---

## 🔹 **Ví dụ Banner Beginner**

* `id: BANNER_BEGINNER`
* Type: Beginner, temporary
* Duration: 7 ngày hoặc 20 pulls
* Pity: hard 10, guaranteedPickupOn 1
* Rates: SSR 5%, SR 25%, R 70%
* Pool SSR: Fire Dragon / Holy Knight / Shadow Assassin (all pickup)
* Guarantees: firstTenPull → guaranteedSSR
* Bonuses: discount 25%
* MaxPulls: 20

---

# 🧩 **2. Drop Rate Display**

Banner hiển thị tỷ lệ pull:

| Thuộc tính          | Ý nghĩa                   |
| ------------------- | ------------------------- |
| showIndividualRates | Hiển thị từng item        |
| showCumulativeRates | Hiển thị tích lũy         |
| includeHistory      | Hiển thị lịch sử pull     |
| legalCompliance     | Chấp hành luật TQ, JP, KR |

---

# 🧩 **3. Wish System**

* `enabled`: false (hiện chưa dùng)
* `maxWishes`: 5
* `wishPool`: danh sách items có thể wish

---

# 🏗 **4. Tổng hợp**

* Hệ thống **multi-banner, multi-type**: Permanent, Limited, Beginner
* Hỗ trợ **pity system** phức tạp: soft/hard, carryOver, pickup guarantee
* Hỗ trợ **bonus / discount**: first pull, daily, generic
* Tuân thủ **display & legal compliance**
* Dễ mở rộng banner mới, item pool, pickup rules, thời gian diễn ra

---
