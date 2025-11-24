
---

# 🎯 **Player Segmentation & AB Testing Overview**

File này quản lý **phân loại người chơi**, **điều chỉnh trải nghiệm**, và **AB testing** cho các nhóm người chơi khác nhau.

---

## 1️⃣ **Segments (Nhóm người chơi)**

Mỗi segment có: `id`, `meta` (tên, mô tả, category, priority), `criteria` (điều kiện), `overrides` (tùy chỉnh gameplay/shop/rewards), `active` (kích hoạt).

### Các segment chính:

1. **New Players**

   * AccountAge ≤ 7 ngày, Level ≤ 20
   * **Overrides:** giảm độ khó stage, giảm sát thương enemy, tăng gold/exp, hiển thị starter packs, special offer “OFFER_BEGINNER_BUNDLE”

2. **F2P Grinders**

   * Chưa chi tiền, accountAge ≥ 7 ngày, dailyPlaytime ≥ 60 phút, login trong 7 ngày
   * **Overrides:** low price shop offers, daily login & achievement bonus, gacha soft pity giảm, đảm bảo SSR lần đầu

3. **Dolphins (Light Spenders)**

   * Chi $5–$50/tháng, accountAge ≥ 14, mua gần đây
   * **Overrides:** special shop offers, VIP benefits, stage difficulty giảm nhẹ, access event VIP dungeon

4. **Whales (High Spenders)**

   * Chi ≥ $50/tháng, accountAge ≥ 14, mua gần đây
   * **Overrides:** premium shop offers, stage difficulty giảm, reward tăng, event VIP dungeon + whale raid, ưu tiên support

5. **Churned Players**

   * Không login ≥ 7 ngày, accountAge ≥ 7
   * **Overrides:** comeback rewards (Gem 500, Scroll 10, Energy 200), push notifications, special events

6. **Competitive PvP Players**

   * ArenaMatches ≥ 20/tuần, ArenaRank ≥ Gold, winRate ≥ 50%
   * **Overrides:** special shop offers, arena currency bonus, ưu tiên matchmaking, access ranked tournament

7. **Beta Testers**

   * BetaTester = true
   * **Overrides:** early access feature, debug tools, testing compensation (Gem + Exclusive title), direct feedback channel

---

## 2️⃣ **Segmentation Rules**

* **Evaluation Frequency:** 3600s (1 giờ)
* **Priority Strategy:** `HighestPriorityWins`
* **Allow Multiple Segments:** true, tối đa 3 segment/user
* **Cache Duration:** 1800s (30 phút)

✅ Cho phép **dynamic assignment** theo hành vi người chơi, ưu tiên nhóm quan trọng nhất.

---

## 3️⃣ **AB Test Groups**

* **Enabled:** true
* Ví dụ: `AB_TEST_GACHA_RATES_V2`

  * Test 2% vs 3% SSR rates cho **New Players**
  * **Start/End Date:** 1–15 Dec 2025
  * **Variants:**

    * Control 50% → SSR 2%
    * TestA 50% → SSR 3%
  * **Metrics:** retention D7, conversion rate, gacha pull count

✅ Cho phép **so sánh hành vi người chơi** theo các thay đổi cụ thể.

---

## 4️⃣ **Fallback Behavior**

* **Segmentation Failure:** dùng default
* **Override Error:** bỏ qua override
* **Logging & Metrics:** bật

✅ Đảm bảo **game không bị lỗi** khi phân đoạn hoặc override không thành công.

---

### 🔹 **Tóm tắt**

* **Segments:** phân loại người chơi theo hành vi, chi tiêu, level, thời gian chơi
* **Overrides:** điều chỉnh gameplay, rewards, shop, events, support theo segment
* **AB Tests:** test các thay đổi ảnh hưởng tới nhóm segment cụ thể
* **Fallback:** an toàn khi lỗi phân đoạn hoặc override

---
