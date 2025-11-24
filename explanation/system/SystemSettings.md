
---

# 🎮 **Game Client & Server Configuration Overview**

File này quản lý **cấu hình toàn bộ hệ thống game**, bao gồm **server, client, gameplay, economy, social, PVP, performance, notifications, security, analytics, logging, localization, regions và debug**.

---

## 1️⃣ **Server**

* `environment`: Production
* `apiVersion`: v1
* `baseUrl` / `cdnUrl`: địa chỉ API & CDN
* `timeout`: 30000ms
* `retryAttempts`: 3, `retryDelay`: 1000ms

✅ Quản lý kết nối và retry logic.

---

## 2️⃣ **Client**

* `minSupportedVersion` / `currentVersion` / `forceUpdateVersion`
* `updateCheckInterval`: 3600s (1h)
* `maintenanceMode`: bật/tắt, thông báo, estimatedDuration

✅ Kiểm soát cập nhật, bảo trì.

---

## 3️⃣ **Gameplay**

* `maxTeamSize`: 5, `maxCharacterLevel`: 80, `maxItemLevel`: 90
* **EnergyConfig:** maxEnergy 200, regen 1 energy / 300s, naturalCap 100
* **BattleConfig:** maxTurnLimit 50, turnTimeout 30s, unlock autoPlay/SpeedUp theo level
* **InventoryConfig:** maxCharacters 200, maxItems 500, maxMaterials 9999, autoSellThreshold null

✅ Giới hạn gameplay, năng lượng, battle, inventory.

---

## 4️⃣ **Economy**

* `goldCap` / `gemCap`: 999,999,999
* Shop refresh times: daily & weekly
* **GachaConfig:** single/multi pull cost, multiPullCount, discount, historyRetention

✅ Quản lý tiền tệ, gacha, shop.

---

## 5️⃣ **Social**

* `maxFriends` 100, `maxFriendRequests` 50
* `friendStaminaGift`: 10, `maxDailyFriendGifts`: 50
* Chat: max 200 chars, history limit 100, report cooldown 300s

✅ Kiểm soát tính năng bạn bè và chat.

---

## 6️⃣ **PvP**

* `dailyFreeMatches` 5, `energyCostPerMatch` 0
* `matchmakingTimeout`: 60s
* `seasonDuration`: 30 ngày
* `rankDecayEnabled`: true, decayDays 7, decayAmount 50

✅ PvP rules, rank decay, energy.

---

## 7️⃣ **Performance**

* `targetFrameRate`: 60 FPS
* **GraphicsQuality:** low/medium/high settings (shadow, texture, particle)
* AssetBundle compression & caching, max concurrent downloads 3

✅ Điều chỉnh đồ họa & tải asset tối ưu.

---

## 8️⃣ **Notifications**

* **PushNotifications:** energyFull, eventStart, guild, friendRequests
* **InGameNotifications:** maintenance, events, updates, achievements

✅ Quản lý thông báo.

---

## 9️⃣ **Security**

* **AntiCheat:** client/server/ checksum validation
* **RateLimit:** maxRequestsPerMinute 60, maxRequestsPerHour 1000
* `sessionTimeout` 86400s, `tokenRefreshInterval` 3600s

✅ Bảo vệ game chống gian lận & lạm dụng API.

---

## 🔟 **Analytics**

* `enabled`: true, `trackingLevel`: Detailed
* `batchSize` 50, `uploadInterval` 60s, offlineQueueSize 1000
* `anonymousTracking`: false

✅ Thu thập dữ liệu hành vi người chơi.

---

## 1️⃣1️⃣ **Logging**

* `logLevel`: Info
* Remote logging enabled, maxLocalLogs 100
* `sensitiveDataMasking` true, crashReporting enabled

✅ Quản lý log & crash reporting.

---

## 1️⃣2️⃣ **Localization**

* `defaultLanguage`: en, supported: en/zh/ko
* Auto-detect, fallback en, cache enabled, hot reload
* Format support: date/time/number/currency/pluralization/parameters
* Validation: maxTextLength 1000, warn missing keys

✅ Hỗ trợ đa ngôn ngữ, cache & validation.

---

## 1️⃣3️⃣ **Regions**

* `default`: Global
* Available: Global, Asia, EU, NA với serverUrl tương ứng

✅ Hệ thống region server.

---

## 1️⃣4️⃣ **Debug**

* EnableDebugMenu, CheatCodes, GodMode, skipTutorial, showFPS/memoryUsage → false

✅ Chế độ debug tắt trên production.

---
