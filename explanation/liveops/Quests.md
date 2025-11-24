
---

# 📜 **Quests Overview**

File JSON này định nghĩa tất cả **nhiệm vụ (quests)** trong game. Mỗi nhiệm vụ bao gồm:

* **Meta info**: tên, mô tả, icon, category (daily, weekly, story, event), displayOrder
* **Requirements**: minLevel, unlockCondition
* **Objectives**: các task cần hoàn thành, event listener, filter, số lần cần đạt
* **Rewards**: item, currency, fragments…
* **ChainQuest**: nhiệm vụ kế tiếp (nếu có)

Ngoài ra, JSON còn định nghĩa **questCategories** để quy định reset, maxActive và liên kết với event.

---

# 🧩 **1. Quest Categories**

| Category | Reset                  | Max Active | Notes                            |
| -------- | ---------------------- | ---------- | -------------------------------- |
| daily    | Time: 00:00 UTC        | 10         | Reset hàng ngày                  |
| weekly   | Day 1, Time: 00:00 UTC | 10         | Reset hàng tuần                  |
| story    | persistent             | n/a        | Nhiệm vụ cốt truyện, không reset |
| event    | linkedToEvents         | n/a        | Nhiệm vụ liên quan event         |

---

# 🔹 **2. Các Quest Tiêu Biểu**

### **2.1 DAILY_COMPLETE_STAGES**

* Category: daily
* Requirements: minLevel 1
* Objectives: Hoàn thành **5 stages bất kỳ**
* Rewards: 5000 Gold, 20 Energy

---

### **2.2 DAILY_ARENA_BATTLES**

* Category: daily
* Requirements: minLevel 15, unlockCondition: ARENA_UNLOCKED
* Objectives: **Thắng 3 trận Arena PvP**
* Rewards: 100 Arena Coin, 20 Gems

---

### **2.3 WEEKLY_ENHANCE_ITEMS**

* Category: weekly
* Requirements: minLevel 5
* Objectives: **Nâng cấp 20 items bất kỳ**
* Rewards: 50,000 Gold, 10 Weapon Ore

---

### **2.4 WEEKLY_BOSS_HUNT**

* Category: weekly
* Requirements: minLevel 10
* Objectives: **Đánh bại 10 Boss**
* Rewards: 200 Gems, 5 Rare Ore

---

### **2.5 STORY_CHAPTER_1**

* Category: story
* Requirements: minLevel 1
* Objectives: **Clear Stage 1 Boss**
* Rewards: 100 Gems, 30 Shadow Assassin Fragments
* ChainQuest: STORY_CHAPTER_2 (nhiệm vụ kế tiếp mở sau khi hoàn thành)

---

# 🔹 **3. Objective Types**

| Type            | Event Listener  | Filters        | Required Count |
| --------------- | --------------- | -------------- | -------------- |
| StageCompletion | OnStageComplete | stageType: Any | 5              |
| PvPVictory      | OnPvPVictory    | mode: Arena    | 3              |
| ItemEnhancement | OnItemEnhance   | itemType: Any  | 20             |
| EnemyKill       | OnEnemyKilled   | tag: Boss      | 10             |
| SpecificStage   | OnStageComplete | stageId        | 1              |

---

# 🔹 **4. Summary**

* Hệ thống quest **đa dạng**: Daily, Weekly, Story, Event
* **Reset & maxActive** cho daily/weekly
* **Chain quest** hỗ trợ cốt truyện tuyến tính
* **Event-driven**: objectives lắng nghe sự kiện gameplay để cập nhật tiến trình
* Rewards: Gold, Gems, Energy, Fragments, Materials…

---
