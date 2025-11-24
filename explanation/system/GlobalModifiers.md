
---

# ⚡ **Overview: Modifiers System**

File này định nghĩa **tất cả các modifier (buffs, event bonuses, gameplay multipliers)** trong game. Nó bao gồm:

* **modifierTemplates**: các template modifier khả dụng.
* **activeModifiers**: danh sách modifier đang kích hoạt (hiện đang rỗng).
* **operationTypes**: các loại phép toán áp dụng cho modifier (Add, Multiply, Set…).
* **scopeTypes**: phạm vi áp dụng modifier (Global, Event, Raid…).
* **activationRules**: quy tắc áp dụng, xếp chồng, và hết hạn modifier.
* **monitoring**: theo dõi usage và impact của modifiers.

---

# 🧩 **1. Modifier Templates**

Mỗi modifier có các trường quan trọng:

| Trường                                           | Mô tả                                                              |
| ------------------------------------------------ | ------------------------------------------------------------------ |
| id                                               | ID duy nhất của modifier                                           |
| meta                                             | Name, description key, icon, category (Economy, Progression, PvP…) |
| target                                           | Biến game được ảnh hưởng (Gold, ExpGain, PvPRewards…)              |
| operation                                        | Cách tính toán: Add, Multiply, Set                                 |
| value                                            | Giá trị modifier áp dụng                                           |
| scope                                            | Phạm vi áp dụng: Global, Event, Raid…                              |
| stackable                                        | Có thể stack hay không                                             |
| affectedModes / affectedEvents / affectedBanners | Phạm vi chi tiết hơn (nếu cần)                                     |
| visual                                           | UI/UX hiển thị: badge, màu, particle effect                        |
| limits / disclaimer                              | Một số modifier có giới hạn hoặc cảnh báo                          |

---

## 🔹 **Các Modifier Tiêu Biểu**

| ID                       | Target            | Operation | Value | Scope  | Stackable          | Notes                                            |
| ------------------------ | ----------------- | --------- | ----- | ------ | ------------------ | ------------------------------------------------ |
| MOD_DOUBLE_GOLD          | DropRate_Gold     | Multiply  | 2.0   | Global | false              | Gold 2x, badge + VFX                             |
| MOD_REDUCED_ENERGY_COST  | StaminaCost       | Multiply  | 0.5   | Global | false              | PvE/Dungeon, half energy cost                    |
| MOD_EXP_BOOST            | ExpGain           | Multiply  | 1.5   | Global | true, max 3 stacks | XP boost, golden badge                           |
| MOD_DROP_RATE_UP         | DropRate_All      | Multiply  | 1.3   | Global | false              | PvE, Dungeon, Raid, item drop boost              |
| MOD_PVP_REWARD_BONUS     | PvPRewards        | Add       | 50    | Global | false              | Arena/RankedPvP rewards bonus                    |
| MOD_SUMMON_RATE_UP       | GachaRate_SSR     | Add       | 0.01  | Global | false              | Standard banner, 1% SSR rate up, with disclaimer |
| MOD_COOLDOWN_REDUCTION   | SkillCooldown     | Multiply  | 0.8   | Global | false              | PvE only, skill cooldown reduction               |
| MOD_RAID_DMG_BOOST       | PlayerDamage      | Multiply  | 1.25  | Raid   | false              | Dragon Raid only, +25% damage                    |
| MOD_FREE_ENERGY_REFILL   | EnergyRefillCost  | Set       | 0     | Global | false              | Free refills max 3/day, resets daily             |
| MOD_ENHANCED_FIRST_CLEAR | FirstClearRewards | Multiply  | 2.0   | Global | false              | PvE first clear rewards double                   |

---

# 🧩 **2. Operation Types**

| Operation | Description                                | Formula             |
| --------- | ------------------------------------------ | ------------------- |
| Add       | Cộng giá trị modifier vào base             | base + modifier     |
| Multiply  | Nhân base với modifier                     | base * modifier     |
| Set       | Gán giá trị modifier, bỏ qua base          | modifier            |
| Max       | Lấy giá trị lớn nhất giữa base và modifier | max(base, modifier) |
| Min       | Lấy giá trị nhỏ nhất giữa base và modifier | min(base, modifier) |

---

# 🧩 **3. Scope Types**

| Scope   | Mô tả                      |
| ------- | -------------------------- |
| Global  | Tất cả người chơi          |
| Region  | Chỉ vùng cụ thể            |
| Segment | Chỉ nhóm người chơi cụ thể |
| Event   | Chỉ trong event            |
| Raid    | Chỉ trong raid battles     |
| User    | Chỉ với user IDs cụ thể    |

---

# 🧩 **4. Activation Rules**

* maxActiveModifiers: 10
* conflictResolution: HighestPriority
* stackingRules: cùng type → Replace, khác type → Stack
* expiration: autoExpire = true, thông báo 1h trước hết hạn

---

# 🧩 **5. Monitoring / Tracking**

* trackUsage: true
* trackImpact: true
* Metrics theo dõi:

  * METRIC_CURRENCY_EARNED
  * METRIC_CURRENCY_SPENT
  * METRIC_GACHA_PULL_COUNT
  * METRIC_STAGE_COMPLETION

---

📌 **Tóm lại:** Đây là hệ thống modifiers toàn diện cho gameplay, event, loot, progression, PvP, gacha… Có UI/UX hiển thị, stack rules, limit, và monitoring để LiveOps dễ quản lý.

---
