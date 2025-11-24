
---

# 🎉 **Events Overview**

File JSON này định nghĩa tất cả các **sự kiện (events)** trong game. Mỗi sự kiện có:

* **Meta info**: tên, mô tả, icon, type, category
* **Schedule**: thời gian bắt đầu/kết thúc, pattern lặp lại (recurring/seasonal)
* **Requirements**: level tối thiểu, điều kiện unlock
* **Content / Effects**: phần thưởng, modifier, boss, stages…
* **Announcements**: thông báo in-game, popup, thông báo trước khi bắt đầu

Ngoài ra còn có **globalEventConfig** quản lý toàn bộ sự kiện đồng thời, thứ tự ưu tiên, và thông báo.

---

# 🧩 **1. Event Structure**

| Thuộc tính   | Ý nghĩa                                                                                        |
| ------------ | ---------------------------------------------------------------------------------------------- |
| id           | Unique event ID                                                                                |
| meta         | Metadata: nameKey, descriptionKey, icon, type (Recurring/Limited/Permanent/Seasonal), category |
| schedule     | startDate, endDate, recurrence pattern (Daily/Weekly/Monthly), duration                        |
| requirements | minLevel, unlockCondition (stage completed, quest cleared…)                                    |
| content      | stages, rewards, event currency, daily rewards, season pass tiers                              |
| effects      | global modifiers: DropRateMultiplier, Buffs, Debuffs…                                          |
| announcement | in-game popup, push notification, notifyBeforeStart (seconds)                                  |

---

# 🔹 **2. Các Event Tiêu Biểu**

### **2.1 EVENT_DOUBLE_DROP**

* Type: Recurring (Farming)
* Schedule: 25–27 Nov 2025, lặp lại vào **Thứ 6 & Thứ 7**, duration 48h
* Requirements: minLevel 10
* Effects: **DropRateMultiplier x2** cho tất cả stage
* Announcement: popup + notify 1 ngày trước

---

### **2.2 EVENT_DRAGON_RAID**

* Type: Limited (Raid)
* Schedule: 1–15 Dec 2025
* Requirements: minLevel 25 + hoàn thành **STAGE_5_BOSS_CLEARED**
* Content:

  * Stages: Easy / Hard với energyCost, recommendedPower
  * Boss: BOSS_ANCIENT_DRAGON, AI_BOSS_PATTERN
  * Rewards: firstClear & repeat
  * Event Currency: CURRENCY_EVENT_DRAGON_COIN → dùng trong SHOP_EVENT_DRAGON_RAID
  * Milestones: điểm cộng dồn để nhận rewards
* Announcement: popup + notify 2 ngày trước

---

### **2.3 EVENT_LOGIN_REWARD**

* Type: Permanent (Engagement)
* Schedule: bắt đầu 1 Jan 2025, không kết thúc
* Content: daily rewards 7 ngày, reset weekly
* Announcement: in-game show, popup false
* Rewards: Gold, Gems, Summon Scroll, exclusive avatar 7 ngày

---

### **2.4 EVENT_SEASON_PASS**

* Type: Seasonal (Progression)
* Schedule: 1–30 Nov 2025, lặp lại monthly
* Requirements: minLevel 10
* Content:

  * Tiers: tier 1 / 10 / 30, free & premium rewards
  * Exp Sources: DailyQuest, WeeklyQuest, StageCompletion
  * PremiumPass: price 980 gems, iapSku: com.game.season.pass.premium
* Announcement: popup + notify 3 ngày trước

---

# 🏗 **3. Global Event Configuration**

| Thuộc tính           | Ý nghĩa                                                                 |
| -------------------- | ----------------------------------------------------------------------- |
| maxConcurrentEvents  | Tối đa 5 sự kiện chạy cùng lúc                                          |
| priorityOrder        | Thứ tự ưu tiên khi conflict: Limited > Seasonal > Recurring > Permanent |
| conflictResolution   | Khi nhiều event xung đột → dùng **Priority**                            |
| notificationSettings | pushNotifications, inGamePopups, mailboxMessages                        |

---

# 🔹 **4. Tổng hợp**

* Hệ thống event **đa dạng**: Recurring, Limited, Permanent, Seasonal
* **Hỗ trợ multi-layer rewards**: daily, milestone, first clear, repeat, tiers
* **Sử dụng event currency**: tích lũy & tiêu trong shop sự kiện
* **Announcement & notification** tích hợp: popup, push, notifyBeforeStart
* **Global management**: maxConcurrent, priority, conflictResolution

---
