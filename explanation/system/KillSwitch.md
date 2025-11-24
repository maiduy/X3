
---

# ⚙️ **Overview: Server & Operations Config**

File này định nghĩa **cấu hình vận hành game**, bao gồm:

* **Maintenance / Emergency Mode**: tắt/mở server, thông báo cho người chơi.
* **Disabled Content & Features**: tạm thời vô hiệu hóa các tính năng, nhân vật, vật phẩm, stage, banner, quest, v.v.
* **Force Update & Hotfixes**: yêu cầu cập nhật client hoặc áp dụng hotfix giá trị.
* **Rate Limits & Anti-Abuse**: giới hạn thao tác và phát hiện gian lận/spam.
* **Server Health & Scaling**: theo dõi CPU, memory, auto-scaling, degraded mode.
* **Feature Gates**: bật/tắt từng tính năng (PVP, Gacha, Guild, Chat…).
* **Fallback & Defaults**: cấu hình dự phòng nếu server hoặc network gặp vấn đề.
* **Monitoring & Notifications**: báo cáo lỗi, tracking performance, gửi thông báo cho người chơi.

---

# 🧩 **1. Maintenance & Emergency Mode**

| Trường                                        | Mô tả                              |
| --------------------------------------------- | ---------------------------------- |
| isActive                                      | true → server đang bảo trì         |
| message / messageKey                          | thông báo hiển thị người chơi      |
| estimatedEndTime                              | thời gian dự kiến kết thúc bảo trì |
| allowedUsers / allowedIPs / allowTestAccounts | người chơi/IP được phép truy cập   |
| redirectUrl                                   | chuyển hướng nếu cần               |

**Emergency Mode**:

* enabled: true/false
* level: None / Low / Medium / High
* actions: disableIAP, disablePvP, disableGacha, disableChat, readOnlyMode
* message: thông báo khi game gặp sự cố

---

# 🧩 **2. Disabled Content & Features**

Có thể tạm thời vô hiệu hóa:

* Features: `disabledFeatures`
* Characters: `disabledCharacters`
* Items, Skills, Stages, Banners, Products, Events, Quests

---

# 🧩 **3. Force Update & Hotfixes**

**ForceUpdateRequired**:

* enabled: true/false
* minVersion: version tối thiểu yêu cầu
* message + storeUrl: thông báo và link cập nhật

**HotfixValues**:

* các flag hoặc giá trị nhanh có thể bật/tắt mà không cần release mới
* ví dụ: `global_chat_enabled`, `energy_refill_enabled`, `daily_login_enabled`…

---

# 🧩 **4. Rate Limits & Anti-Abuse**

**RateLimit**: giới hạn số thao tác/minute

| Action         | Limit  |
| -------------- | ------ |
| API requests   | 60/min |
| Gacha pulls    | 10/min |
| Shop purchases | 5/min  |
| Battle starts  | 10/min |

**AntiAbuse**:

* enableSpamDetection / DupeDetection / CheatDetection
* suspiciousActivityThreshold: 10
* autoBlockEnabled: false

---

# 🧩 **5. Server Health & Auto-Scaling**

| Metric       | Threshold |
| ------------ | --------- |
| CPU          | 80%       |
| Memory       | 85%       |
| DegradedMode | 90%       |

* autoScaleEnabled: true → tự động scale server khi cần

---

# 🧩 **6. Feature Gates**

Cho phép bật/tắt từng tính năng:

| Feature      | Enabled | Reason / DisabledSince              |
| ------------ | ------- | ----------------------------------- |
| PVP Arena    | true    | null                                |
| Guild System | false   | Under development, since 2025-11-20 |
| Chat System  | true    | null                                |
| Gacha        | true    | null                                |
| IAP          | true    | null                                |

---

# 🧩 **7. Content Blacklist**

* bannedWords, bannedUserIds, bannedDeviceIds, restrictedRegions

---

# 🧩 **8. Fallback Config / Defaults**

* useLocalCache: true, maxCacheAge: 86400s
* defaultDropRates: SSR 2%, SR 18%, R 80%
* defaultEnergyRegenRate & Interval

---

# 🧩 **9. Monitoring & Notifications**

* enableErrorReporting / PerformanceTracking / UserBehaviorTracking
* samplingRate: 10%
* Notifications: maintenance/emergency, kênh Push/InGame/Email

---
