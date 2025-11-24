
---

# ⚡ **Feature Flags Overview**

File này định nghĩa **tất cả các tính năng bật/tắt (feature flags)** và **AB tests** trong game. Cấu trúc gồm:

* **flags**: các tính năng có thể bật/tắt hoặc rollout theo nhóm, vùng, nền tảng.
* **abTests**: thử nghiệm A/B để đo lường tác động của các thay đổi gameplay hoặc kinh tế.
* **remoteConfig**: cấu hình cập nhật từ server để client load các flags & tests.

---

# 🧩 **1. Feature Flags**

Mỗi flag có các trường quan trọng:

| Trường       | Mô tả                                                      |
| ------------ | ---------------------------------------------------------- |
| id           | ID duy nhất của feature                                    |
| meta         | Tên, mô tả, category (GameMode, Social, Monetization…)     |
| enabled      | True/False, bật hay tắt feature                            |
| rollout      | Chiến lược phân phối: All, Gradual, Beta, RegionBased      |
| requirements | MinVersion, nền tảng, vùng áp dụng                         |
| killSwitch   | Cho phép tắt gấp feature nếu gặp lỗi                       |
| config       | (Tùy chọn) Cấu hình chi tiết, ví dụ: max refills, baseCost |

---

## 🔹 **Các Feature Tiêu Biểu**

| ID                              | Name                       | Enabled | Category     | Rollout                     | Notes                                      |
| ------------------------------- | -------------------------- | ------- | ------------ | --------------------------- | ------------------------------------------ |
| FEATURE_PVP_ARENA               | PvP Arena System           | true    | GameMode     | Gradual 100%                | Bật PvP Arena toàn cầu                     |
| FEATURE_GUILD_SYSTEM            | Guild System               | false   | Social       | Beta 10%                    | Chỉ BetaTesters & VIP                      |
| FEATURE_SEASON_PASS             | Season Pass                | true    | Monetization | All 100%                    | Hệ thống Season Pass                       |
| FEATURE_DAILY_DEALS             | Daily Deals Shop           | true    | Economy      | All 100%                    | Shop deals xoay vòng                       |
| FEATURE_CHAT_SYSTEM             | In-Game Chat               | true    | Social       | RegionBased 100%, Except CN | Chat toàn cầu ngoại trừ Trung Quốc         |
| FEATURE_AUTO_BATTLE             | Auto Battle                | true    | Gameplay     | All 100%                    | AI tự chiến đấu PvE                        |
| FEATURE_ENERGY_REFILL_LIMIT     | Energy Refill Daily Limit  | true    | Economy      | All 100%                    | Giới hạn nạp năng lượng tối đa 10 lần/ngày |
| FEATURE_ENHANCED_ANALYTICS      | Enhanced Analytics         | true    | Analytics    | Gradual 50%                 | Theo dõi hành vi người chơi chi tiết       |
| FEATURE_DOUBLE_DROP_EVENT       | Double Drop Weekend Events | true    | LiveOps      | All 100%                    | Bật double drop vào cuối tuần              |
| FEATURE_CROSS_PLATFORM_PROGRESS | Cross-Platform Progress    | false   | Technical    | Beta 5%                     | Sync tiến độ giữa các nền tảng             |

---

# 🧩 **2. AB Tests**

AB Tests dùng để **thử nghiệm các biến thể gameplay/economy**.

### **2.1 AB_TEST_GACHA_RATES**

* Name: Gacha Rate Test
* Description: Test tỷ lệ SSR cho người chơi mới
* Thời gian: 2025-12-01 → 2025-12-15
* Variants:

  * Control: 50%, SSR 0.02
  * TestA: 50%, SSR 0.03
* Target Audience: level 1-10, newPlayersOnly
* Metrics đo lường: Retention D7, Conversion Rate, ARPU

---

# 🧩 **3. Remote Config**

* updateFrequency: 300s → client pull update mỗi 5 phút
* cacheDuration: 3600s → lưu cache 1h
* fallbackToLocal: true → nếu server lỗi, dùng config cục bộ

---

# ✅ **Tóm tắt**

* **Feature Flags**: bật/tắt tính năng theo nhóm, vùng, nền tảng, chiến lược rollout khác nhau.
* **KillSwitch**: đảm bảo có thể tắt gấp nếu lỗi.
* **AB Tests**: thử nghiệm phân tách người chơi, đo metric kinh tế và retention.
* **Remote Config**: client tự động update config mà không cần update app.

---
