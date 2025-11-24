# Rewards.json Schema Documentation

## Overview
Defines drop tables, achievement rewards, level-up rewards, first-time bonuses, login streaks, and random reward boxes for the entire game economy.

---

## Root Level Fields

### `dropTables` (array)
Random loot tables referenced by stages and dungeons.

### `achievementRewards` (array)
One-time rewards for completing achievements.

### `levelUpRewards` (array)
Rewards granted when player reaches specific levels.

### `firstTimeRewards` (object)
Special bonuses for first-time actions.

### `streakRewards` (object)
Rewards for consecutive login/win streaks.

### `randomRewardBoxes` (array)
Gacha-style reward containers with weighted items.

---

## Drop Table Object

### `id` (string)
- **Format**: `DROP_TABLE_[CATEGORY]`
- **Example**: `"DROP_TABLE_CHAPTER_1_NORMAL"`, `"DROP_TABLE_BOSS_CHAPTER_1"`
- **Referenced by**: Levels.json stages

### `meta` (object)

#### `name` (string)
- **Example**: `"Chapter 1 Normal Drop Table"`

#### `description` (string)
- **Example**: `"Common drops for Chapter 1 normal difficulty stages"`

### `drops` (array)
Array of possible drop items.

#### Drop Item Object:

##### `itemId` (string)
- **Description**: Item identifier from Items.json
- **Example**: `"MAT_COMMON_ORE"`, `"CURRENCY_GOLD"`

##### `dropRate` (number)
- **Type**: Float (0.0-1.0)
- **Description**: Probability of this item dropping
- **Example**: `0.30` = 30% chance
- **Note**: `1.0` = guaranteed drop

##### `quantity` (object)
- **Structure**: `{"min": number, "max": number}`
- **Description**: Random quantity range
- **Example**: `{"min": 1, "max": 3}` = 1-3 items

---

## Achievement Reward Object

### `id` (string)
- **Format**: `ACHIEVEMENT_[NAME]`
- **Example**: `"ACHIEVEMENT_FIRST_VICTORY"`, `"ACHIEVEMENT_STAGE_MASTER"`

### `meta` (object)

#### `nameKey` (string)
- **Type**: Localization Key
- **Example**: `"ACHIEVEMENT_NAME_FIRST_VICTORY"`

#### `descriptionKey` (string)
- **Type**: Localization Key
- **Example**: `"ACHIEVEMENT_DESC_FIRST_VICTORY"`

#### `icon` (string)
- **Type**: Asset Reference
- **Example**: `"Icon_Achievement_FirstVictory"`

#### `category` (string)
- **Values**: `"Combat"`, `"Collection"`, `"Social"`, `"Progression"`, `"Economy"`
- **Usage**: Achievement grouping in UI

### `requirements` (object)

#### `type` (string)
- **Values**: `"StageCompletion"`, `"GachaPull"`, `"CharacterAscension"`, `"PvPWins"`, `"FriendCount"`
- **Description**: What action triggers achievement

#### `count` (number)
- **Type**: Integer
- **Description**: Number of times action must be performed
- **Example**: `100` = complete 100 stages

### `rewards` (array)
- **Type**: Array of Reward Objects
- **Structure**: `[{"itemId": "...", "amount": number}]`
- **Example**: `[{"itemId": "CURRENCY_GEM", "amount": 500}]`

### `hidden` (boolean)
- **Description**: Whether achievement is visible before unlocking
- **Default**: `false`
- **Usage**: Secret achievements

### `points` (number)
- **Type**: Integer
- **Description**: Achievement points awarded
- **Example**: `10`, `100`, `200`
- **Usage**: Total achievement score tracking

---

## Level Up Rewards Array

### Level Reward Object

#### `level` (number)
- **Type**: Integer
- **Description**: Player level that grants rewards
- **Example**: `5`, `10`, `20`, `30`, `50`

#### `rewards` (array)
- **Type**: Array of Reward Objects
- **Example**:
```json
[
  {"itemId": "CURRENCY_GEM", "amount": 100},
  {"itemId": "ITEM_SUMMON_SCROLL", "amount": 5}
]
```

**Typical Milestones**: `5, 10, 15, 20, 25, 30, 40, 50, 60, 70, 80`

---

## First Time Rewards Object

### `firstTimeRewards` (object)
Special rewards for first-time actions.

#### `firstLogin` (array)
- **Description**: New player welcome bonus
- **Example**: `[{"itemId": "CURRENCY_GOLD", "amount": 50000}, {"itemId": "CURRENCY_GEM", "amount": 500}]`

#### `firstStageComplete` (array)
- **Description**: First battle completion
- **Example**: `[{"itemId": "CURRENCY_GOLD", "amount": 5000}]`

#### `firstGachaPull` (array)
- **Description**: First gacha summon bonus
- **Example**: `[{"itemId": "CURRENCY_GOLD", "amount": 10000}]`

#### `firstPvPWin` (array)
- **Description**: First PvP victory bonus
- **Example**: `[{"itemId": "CURRENCY_ARENA_COIN", "amount": 100}]`

**Usage**: Encourage player engagement with new features.

---

## Streak Rewards Object

### `loginStreak` (object)
Consecutive daily login rewards.

#### `enabled` (boolean)
- **Description**: Whether login streak tracking is active
- **Default**: `true`

#### `rewards` (array)
Array of milestone rewards.

##### Streak Milestone Object:

###### `day` (number)
- **Type**: Integer
- **Description**: Consecutive login days
- **Example**: `3`, `7`, `14`, `30`

###### `rewards` (array)
- **Type**: Array of Reward Objects
- **Example**: Day 7 = `[{"itemId": "CURRENCY_GEM", "amount": 200}, {"itemId": "ITEM_SUMMON_SCROLL", "amount": 5}]`

### `winStreak` (object)
Consecutive PvP win bonus.

#### `enabled` (boolean)
- **Default**: `true`

#### `bonusMultiplier` (number)
- **Type**: Float
- **Description**: Reward multiplier per consecutive win
- **Example**: `1.1` = +10% per win

#### `maxBonus` (number)
- **Type**: Float
- **Description**: Maximum streak multiplier
- **Example**: `2.0` = 200% rewards (double)

#### `appliesToModes` (array)
- **Type**: Array of Mode Names
- **Example**: `["Arena"]`
- **Usage**: Restrict to specific game modes

---

## Random Reward Box Object

### `id` (string)
- **Format**: `REWARD_BOX_[TIER]`
- **Example**: `"REWARD_BOX_BRONZE"`, `"REWARD_BOX_GOLD"`

### `meta` (object)

#### `nameKey` (string)
- **Type**: Localization Key
- **Example**: `"REWARD_BOX_NAME_BRONZE"`

#### `icon` (string)
- **Type**: Asset Reference
- **Example**: `"Icon_RewardBox_Bronze"`

#### `rarity` (string)
- **Values**: `"Common"`, `"Rare"`, `"Epic"`, `"Legendary"`
- **Usage**: Box tier indication

### `contents` (array)
Weighted random item pool.

#### Content Item Object:

##### `itemId` (string)
- **Description**: Possible reward item
- **Example**: `"CURRENCY_GOLD"`

##### `weight` (number)
- **Type**: Integer
- **Description**: Relative probability weight
- **Example**: `50` (higher = more likely)

##### `quantity` (object)
- **Structure**: `{"min": number, "max": number}`
- **Example**: `{"min": 1000, "max": 5000}`

### `guaranteedCount` (number)
- **Type**: Integer
- **Description**: Number of items guaranteed from box
- **Example**: `3` = player gets 3 random items from weighted pool

**Weight System**:
```
Total Weight = Sum of all item weights
Item Chance = Item Weight / Total Weight

Example:
Item A: weight 50, chance = 50/100 = 50%
Item B: weight 30, chance = 30/100 = 30%
Item C: weight 20, chance = 20/100 = 20%
```

---

## Drop Rate Calculation Example

### Stage Drop Example
```
Drop Table: DROP_TABLE_CHAPTER_1_NORMAL

Drops:
- MAT_COMMON_ORE: 30% chance, 1-3 qty
- MAT_WEAPON_ORE: 15% chance, 1-2 qty
- CURRENCY_GOLD: 100% chance, 50-150 qty
- ITEM_EXP_POTION_SMALL: 20% chance, 1-2 qty

Player completes stage:
1. Roll GOLD: 100% → guaranteed 50-150 gold
2. Roll COMMON_ORE: 30% → if success, get 1-3 ore
3. Roll WEAPON_ORE: 15% → if success, get 1-2 ore
4. Roll EXP_POTION: 20% → if success, get 1-2 potions

Result: Always get gold, may get 0-3 additional items
```

---

## Achievement Progression Example

### Combat Achievements
```
ACHIEVEMENT_FIRST_VICTORY
- Type: StageCompletion, Count: 1
- Rewards: 50 gems, 5000 gold
- Points: 10

ACHIEVEMENT_STAGE_MASTER
- Type: StageCompletion, Count: 100
- Rewards: 500 gems, 10 scrolls, exclusive title
- Points: 100

Total Points for 100 stages: 110 points
```

---

## Login Streak Example

### 30-Day Streak Rewards
```
Day 3: 50 gems
Day 7: 200 gems + 5 scrolls
Day 14: 500 gems + 10 scrolls + exclusive avatar
Day 30: 1000 gems + 30 scrolls + SSR equipment

Total 30-day value: 1750 gems + 45 scrolls + exclusives
```

---

## Data Validation Rules

1. **Drop Rates**: All dropRate values must be 0.0-1.0
2. **Quantity Ranges**: min ≤ max in all quantity objects
3. **Item References**: All itemIds must exist in Items.json
4. **Weight Positivity**: All weights must be positive integers
5. **Level Ordering**: levelUpRewards should be in ascending order
6. **Streak Days**: loginStreak days should be in ascending order
7. **Guaranteed Count**: guaranteedCount ≤ contents array length
8. **Achievement Points**: Points should scale with difficulty

---

## Related Configuration Files

- **Items.json**: All reward itemIds reference this
- **Levels.json**: Stages reference drop tables
- **Quests.json**: Quest rewards follow same structure
- **ArenaConfig.json**: PvP rewards use streak system
- **Characters.json**: Character fragments in rewards

---

## Version History
- **1.0.0** (2025-11-23): Initial schema documentation
