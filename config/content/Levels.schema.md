# Levels.json Schema Documentation

## Overview
This document provides comprehensive descriptions for all indices (fields) in the `Levels.json` configuration file. This file defines PvE content including story campaigns, chapters, stages, dungeons, enemy waves, victory conditions, rewards, drop tables, and star rating systems.

---

## Root Level Fields

### `$schema` (string)
- **Type**: URI
- **Description**: JSON Schema version reference for validation
- **Example**: `"https://json-schema.org/draft/2020-12/schema"`

### `version` (string)
- **Type**: Semantic Version
- **Description**: Configuration file version for compatibility tracking
- **Format**: `MAJOR.MINOR.PATCH`
- **Example**: `"1.0.0"`

### `lastUpdated` (string)
- **Type**: ISO 8601 DateTime
- **Description**: Timestamp of last configuration update
- **Format**: `YYYY-MM-DDTHH:mm:ssZ`
- **Example**: `"2025-11-23T00:00:00Z"`

### `campaigns` (array)
- **Type**: Array of Campaign Objects
- **Description**: Story campaigns with linear progression
- **Required**: Yes
- **Usage**: Main story content

### `dungeons` (array)
- **Type**: Array of Dungeon Objects
- **Description**: Repeatable resource/material farming stages
- **Required**: Yes
- **Usage**: Daily farming content

---

## Campaign Object Structure

### `id` (string)
- **Type**: Unique Identifier
- **Description**: Campaign identifier
- **Format**: `CAMPAIGN_[NAME]`
- **Example**: `"CAMPAIGN_MAIN_STORY"`
- **Required**: Yes

---

## Meta Object (Campaign Metadata)

### `meta` (object)

#### `meta.nameKey` (string)
- **Type**: Localization Key
- **Description**: Reference key for campaign's display name
- **Format**: `CAMPAIGN_NAME_[CAMPAIGN_ID]`
- **Example**: `"CAMPAIGN_NAME_MAIN_STORY"`

#### `meta.descriptionKey` (string)
- **Type**: Localization Key
- **Description**: Reference key for campaign description
- **Format**: `CAMPAIGN_DESC_[CAMPAIGN_ID]`
- **Example**: `"CAMPAIGN_DESC_MAIN_STORY"`

#### `meta.icon` (string)
- **Type**: Asset Reference
- **Description**: Campaign icon sprite
- **Example**: `"Icon_Campaign_MainStory"`

#### `meta.chapters` (number)
- **Type**: Integer
- **Description**: Total number of chapters in campaign
- **Example**: `10`
- **Usage**: UI display, progress tracking

#### `meta.unlockLevel` (number)
- **Type**: Integer
- **Description**: Minimum player level to access campaign
- **Default**: `1` for main story
- **Example**: `1`

---

## Chapters Array

### `chapters` (array)
- **Type**: Array of Chapter Objects
- **Description**: Campaign divided into chapters
- **Ordering**: Sequential, chapter 1 to N

---

## Chapter Object

### `id` (string)
- **Type**: Unique Identifier
- **Description**: Chapter identifier
- **Format**: `CHAPTER_[NUMBER]`
- **Example**: `"CHAPTER_01"`

### `meta` (object)
Chapter metadata.

#### `meta.nameKey` (string)
- **Type**: Localization Key
- **Description**: Chapter display name
- **Example**: `"CHAPTER_01_NAME"`

#### `meta.chapterNumber` (number)
- **Type**: Integer
- **Description**: Chapter number for ordering
- **Example**: `1`

#### `meta.storyUnlock` (string)
- **Type**: Story Event ID
- **Description**: Story cutscene/dialogue unlocked by this chapter
- **Example**: `"STORY_PROLOGUE"`
- **Usage**: Links to story system

### `stages` (array)
- **Type**: Array of Stage Objects
- **Description**: Stages within this chapter
- **Typical Count**: 8-12 stages + 1 boss stage

---

## Stage Object

### `id` (string)
- **Type**: Unique Identifier
- **Description**: Stage identifier
- **Format**: `STAGE_[CHAPTER]_[STAGE]` or `STAGE_[CHAPTER]_BOSS`
- **Examples**:
  - `"STAGE_1_1"` - Normal stage
  - `"STAGE_1_BOSS"` - Boss stage
- **Required**: Yes
- **Constraints**: Must be unique

---

## Meta Object (Stage Metadata)

### `meta` (object)

#### `meta.nameKey` (string)
- **Type**: Localization Key
- **Description**: Stage display name
- **Format**: `STAGE_[CHAPTER]_[NUMBER]_NAME`
- **Example**: `"STAGE_1_1_NAME"`

#### `meta.stageNumber` (number)
- **Type**: Integer
- **Description**: Stage number within chapter
- **Example**: `1`, `2`, ... `10`

#### `meta.difficulty` (string)
- **Type**: Difficulty Enum
- **Description**: Stage difficulty category
- **Values**:
  - `"Normal"` - Standard stages
  - `"Hard"` - Challenge stages
  - `"Hell"` - Extreme difficulty
  - `"Boss"` - Boss encounter
- **Example**: `"Normal"`

#### `meta.recommendedPower` (number)
- **Type**: Integer
- **Description**: Suggested team power for comfortable clear
- **Example**: `500`
- **Usage**: Difficulty indicator for players

#### `meta.isBossStage` (boolean | omitted)
- **Type**: Boolean
- **Description**: Whether this is a boss stage
- **Optional**: Only include if true
- **Example**: `true`

---

## Requirements Object

### `requirements` (object)
Stage unlock and cost requirements.

#### `unlockCondition` (string | null)
- **Type**: Condition Identifier or Null
- **Description**: Prerequisite for accessing this stage
- **Values**:
  - `null` - No prerequisite (first stage)
  - Stage ID - Previous stage must be cleared
- **Example**: `"STAGE_1_1_CLEARED"`

#### `minLevel` (number)
- **Type**: Integer
- **Description**: Minimum player level to attempt
- **Example**: `1`, `2`, `5`

#### `energyCost` (number)
- **Type**: Integer
- **Description**: Energy consumed to start stage
- **Typical Values**:
  - `6` - Normal stages
  - `12` - Boss stages
  - `10-20` - Dungeon stages
- **Usage**: Resource management, farming limits

---

## Map Config Object

### `mapConfig` (object)
Visual and environmental settings.

#### `mapId` (string)
- **Type**: Map Asset Identifier
- **Description**: Battle arena map to use
- **Format**: `MAP_[NAME]`
- **Example**: `"MAP_FOREST_CLEARING"`
- **Usage**: References map asset bundle

#### `environment` (string)
- **Type**: Environment Type Enum
- **Description**: Environmental theme/biome
- **Values**: `"Forest"`, `"Cave"`, `"Desert"`, `"Snow"`, `"Castle"`, `"Hell"`
- **Example**: `"Forest"`
- **Usage**: Visual theming, potential mechanics

#### `weatherEffect` (string | null)
- **Type**: Weather Effect ID or Null
- **Description**: Active weather during battle
- **Values**:
  - `null` - No weather
  - `"Rain"` - Reduces fire damage
  - `"Snow"` - Reduces speed
  - `"Sandstorm"` - Reduces accuracy
- **Example**: `null`
- **Usage**: Environmental modifiers

---

## Enemies Object

### `enemies` (object)
Enemy encounter configuration.

#### `waves` (array)
- **Type**: Array of Wave Objects
- **Description**: Sequential enemy waves in stage
- **Typical Count**: 1-3 waves

---

## Wave Object

### `waveNumber` (number)
- **Type**: Integer
- **Description**: Wave sequence number
- **Example**: `1`, `2`, `3`
- **Usage**: Waves appear in order

### `units` (array)
- **Type**: Array of Enemy Unit Objects
- **Description**: Enemies in this wave

---

## Enemy Unit Object

#### `enemyId` (string)
- **Type**: Enemy Identifier
- **Description**: Enemy character/monster ID
- **Format**: `ENEMY_[NAME]` or `BOSS_[NAME]`
- **Examples**:
  - `"ENEMY_GOBLIN_WARRIOR"` - Normal enemy
  - `"BOSS_GOBLIN_KING"` - Boss
- **References**: Enemy definitions (separate config)

#### `level` (number)
- **Type**: Integer
- **Description**: Enemy's level
- **Range**: 1-100+
- **Example**: `5`
- **Usage**: Determines enemy stats

#### `position` (number)
- **Type**: Integer
- **Description**: Battle formation position (0-4)
- **Format**: `0` = front-left, `4` = back-right
- **Example**: `2` (center)
- **Usage**: Tactical positioning

**Formation Layout**:
```
Position: 0   1   2   3   4
Row:    Front   Mid  Back
```

#### `isBoss` (boolean | omitted)
- **Type**: Boolean
- **Description**: Whether this unit is a boss
- **Optional**: Only include if true
- **Example**: `true`
- **Usage**: Boss-specific mechanics, rewards

#### `aiProfile` (string | omitted)
- **Type**: AI Profile ID
- **Description**: Custom AI behavior pattern
- **Optional**: Uses default if omitted
- **Example**: `"AI_BOSS_PATTERN"`
- **Usage**: Boss mechanics, special behaviors

---

## Victory Condition Object

### `victoryCondition` (object)
Win condition for stage.

#### `type` (string)
- **Type**: Victory Type Enum
- **Description**: How to win this stage
- **Values**:
  - `"ELIMINATE_ALL"` - Kill all enemies
  - `"KILL_BOSS"` - Kill specific boss
  - `"SURVIVE_TURNS"` - Survive N turns
  - `"PROTECT_TARGET"` - Keep unit alive
- **Example**: `"ELIMINATE_ALL"`
- **Links to**: BattleRules.json victoryConditions

#### `params` (object | null)
- **Type**: Parameter Object
- **Description**: Additional condition parameters
- **Optional**: Not needed for ELIMINATE_ALL
- **Examples**:
  - `{"targetTag": "Boss"}` - For KILL_BOSS
  - `{"turns": 15}` - For SURVIVE_TURNS
  - `null` - For simple conditions

---

## Rewards Object (Stage)

### `rewards` (object)
Reward configuration for stage completion.

#### `firstClear` (array)
- **Type**: Array of Reward Objects
- **Description**: One-time rewards for first completion
- **Generous**: Higher value, includes gems/premium items
- **Example**:
```json
[
  {"itemId": "CURRENCY_GOLD", "amount": 2000},
  {"itemId": "CURRENCY_GEM", "amount": 50},
  {"itemId": "ITEM_SUMMON_SCROLL", "amount": 5}
]
```

#### `repeat` (array)
- **Type**: Array of Reward Objects
- **Description**: Rewards for each subsequent clear
- **Standard**: Lower value, basic resources
- **Example**:
```json
[
  {"itemId": "CURRENCY_GOLD", "amount": 300},
  {"itemId": "MAT_RARE_ORE", "amount": 2}
]
```

#### `exp` (number)
- **Type**: Integer
- **Description**: Character experience points awarded
- **Usage**: Levels up characters in team
- **Example**: `500`

#### `playerExp` (number)
- **Type**: Integer
- **Description**: Player account experience points
- **Usage**: Levels up player account
- **Example**: `100`

---

## Drops Object

### `drops` (object)
Random loot table configuration.

#### `table` (string)
- **Type**: Drop Table Identifier
- **Description**: Drop table ID for random loot
- **Format**: `DROP_TABLE_[CATEGORY]`
- **Examples**:
  - `"DROP_TABLE_CHAPTER_1_NORMAL"` - Normal stages
  - `"DROP_TABLE_BOSS_CHAPTER_1"` - Boss stages
- **References**: Separate drop table configuration

#### `bonus` (object | null)
- **Type**: Bonus Drop Configuration
- **Description**: Special drop modifiers
- **Optional**: Not present for standard drops
- **Example**: `null`

#### `guaranteedRare` (boolean | omitted)
- **Type**: Boolean
- **Description**: Whether at least one rare item is guaranteed
- **Optional**: Only for boss stages
- **Example**: `true`
- **Usage**: Boss stage premium drops

---

## Stars Array

### `stars` (array)
- **Type**: Array of Star Objective Objects
- **Description**: Optional challenge objectives for extra rewards
- **Count**: Typically 3 stars
- **Usage**: Completionist content, bonus rewards

---

## Star Objective Object

#### `condition` (string)
- **Type**: Condition Type Enum
- **Description**: Challenge objective to achieve
- **Values**:
  - `"ClearStage"` - Complete stage (auto-awarded)
  - `"NoDeaths"` - No units die
  - `"ClearUnderNTurns"` - Beat turn limit
  - `"UseElementalAdvantage"` - Use specific element
  - `"NoItems"` - No consumables used
  - `"SingleElement"` - Only one element
- **Example**: `"ClearUnder15Turns"`

#### `reward` (object)
- **Type**: Single Reward Object
- **Description**: Item awarded for achieving this star
- **Structure**: `{"itemId": "...", "amount": N}`
- **Example**: `{"itemId": "CURRENCY_GEM", "amount": 5}`

**Star System**:
- ⭐ Star 1: Clear stage (guaranteed)
- ⭐⭐ Star 2: Challenge objective (optional)
- ⭐⭐⭐ Star 3: Harder challenge (optional)

---

## Dungeon Object Structure

### `id` (string)
- **Type**: Unique Identifier
- **Description**: Dungeon identifier
- **Format**: `DUNGEON_[TYPE]_[NAME]`
- **Examples**:
  - `"DUNGEON_GOLD_VAULT"` - Gold farming
  - `"DUNGEON_EXP_TOWER"` - EXP farming
- **Required**: Yes

---

## Meta Object (Dungeon Metadata)

### `meta` (object)

#### `meta.nameKey` (string)
- **Type**: Localization Key
- **Description**: Dungeon display name
- **Example**: `"DUNGEON_NAME_GOLD_VAULT"`

#### `meta.descriptionKey` (string)
- **Type**: Localization Key
- **Description**: Dungeon description
- **Example**: `"DUNGEON_DESC_GOLD_VAULT"`

#### `meta.icon` (string)
- **Type**: Asset Reference
- **Description**: Dungeon icon
- **Example**: `"Icon_Dungeon_GoldVault"`

#### `meta.type` (string)
- **Type**: Dungeon Type Enum
- **Description**: Dungeon category
- **Values**:
  - `"Resource"` - Gold/EXP farming
  - `"Material"` - Ascension materials
  - `"Equipment"` - Gear farming
  - `"Challenge"` - High difficulty content
- **Example**: `"Resource"`

#### `meta.availability` (object)
Dungeon schedule configuration.

##### `daysOfWeek` (array)
- **Type**: Array of Integers
- **Description**: Days dungeon is available
- **Format**: `1` = Monday, `2` = Tuesday, ..., `7` = Sunday
- **Example**: `[1, 3, 5, 7]` (Mon, Wed, Fri, Sun)
- **Usage**: Daily rotation schedule

##### `unlockLevel` (number)
- **Type**: Integer
- **Description**: Player level requirement
- **Example**: `10`

---

## Difficulties Array (Dungeon)

### `difficulties` (array)
- **Type**: Array of Difficulty Configuration Objects
- **Description**: Multiple difficulty tiers for dungeon
- **Typical Count**: 3 difficulties (Easy, Hard, Hell)

---

## Difficulty Configuration Object

#### `difficulty` (string)
- **Type**: Difficulty Name
- **Description**: Difficulty tier name
- **Values**: `"Easy"`, `"Normal"`, `"Hard"`, `"Hell"`, `"Nightmare"`
- **Example**: `"Hard"`

#### `recommendedPower` (number)
- **Type**: Integer
- **Description**: Suggested team power
- **Example**: `5000`

#### `energyCost` (number)
- **Type**: Integer
- **Description**: Energy required
- **Range**: 10-30
- **Example**: `15`

#### `enemyLevel` (number)
- **Type**: Integer
- **Description**: Level of enemies in dungeon
- **Example**: `30`

#### `rewards` (object)
- **Type**: Rewards Configuration
- **Description**: Guaranteed and random rewards

##### `guaranteed` (array)
- **Type**: Array of Reward Objects
- **Description**: Always received items
- **Example**: `[{"itemId": "CURRENCY_GOLD", "amount": 15000}]`

##### `random` (array | omitted)
- **Type**: Array of Reward Objects
- **Description**: Chance-based additional rewards
- **Optional**: Can be empty array
- **Example**: `[]`

##### `exp` (number | omitted)
- **Type**: Integer
- **Description**: Character experience awarded
- **Optional**: For EXP dungeons
- **Example**: `15000`

---

## Daily Limit Field

### `dailyLimit` (number)
- **Type**: Integer
- **Description**: Maximum runs per day
- **Example**: `3`
- **Usage**: Prevents excessive farming
- **Reset**: Daily at midnight

---

## Stage Progression Example

### Chapter 1 Walkthrough

**Stage 1-1** (Entry):
- Unlock: None (first stage)
- Energy: 6
- Enemies: 2 waves, goblins level 3-5
- First Clear: 500 gold, 10 gems, summon scroll
- Stars: Clear (5 gems), No Deaths (200 gold), <15 turns (3 ore)

**Stage 1-2** (Follow-up):
- Unlock: STAGE_1_1_CLEARED
- Energy: 6
- Enemies: 2 waves, more goblins + brute
- First Clear: 600 gold, 10 gems, 5 weapon ore
- Stars: Clear (5 gems), No Deaths (250 gold), <12 turns (3 weapon ore)

**Stage 1-9** (Pre-Boss):
- Unlock: STAGE_1_8_CLEARED
- Energy: 8
- Enemies: Elite enemies preparing for boss

**Stage 1-BOSS** (Chapter Finale):
- Unlock: STAGE_1_9_CLEARED
- Energy: 12 (double cost)
- Enemies: Goblin King (level 10 boss) + 2 guards
- Victory: Kill boss specifically
- First Clear: 2000 gold, 50 gems, 5 scrolls, 10 character fragments
- Stars: Clear (20 gems), No Deaths (5 fragments), <20 turns (5 rare ore)
- Unlocks: Chapter 2

---

## Energy Economy

### Energy Costs
- **Normal Stages**: 6-8 energy
- **Boss Stages**: 10-15 energy
- **Easy Dungeons**: 10 energy
- **Hard Dungeons**: 15 energy
- **Hell Dungeons**: 20-30 energy

### Energy Recovery
- Natural: 1 energy per 5 minutes (288/day)
- Typical max: 150-200 energy
- Allows: 20-30 stage runs per day

---

## Drop Table System

### Drop Table References

**Example**: `"DROP_TABLE_CHAPTER_1_NORMAL"`

**Structure** (defined elsewhere):
```json
{
  "id": "DROP_TABLE_CHAPTER_1_NORMAL",
  "drops": [
    {"itemId": "MAT_COMMON_ORE", "chance": 0.50, "amount": {"min": 1, "max": 3}},
    {"itemId": "MAT_WEAPON_ORE", "chance": 0.20, "amount": {"min": 1, "max": 2}},
    {"itemId": "ITEM_GEAR_R", "chance": 0.15},
    {"itemId": "ITEM_GEAR_SR", "chance": 0.05}
  ]
}
```

---

## Star Rating System

### Completion Tracking

**3-Star System**:
- ⭐ Star 1: Basic completion
- ⭐⭐ Star 2: Skill requirement (no deaths)
- ⭐⭐⭐ Star 3: Speed requirement (turn limit)

**Benefits**:
- Extra rewards per star
- Completionist achievement
- Unlock perfect clear bonuses
- Collection tracking

---

## Data Validation Rules

1. **Unique IDs**: All campaign, chapter, stage, dungeon IDs unique
2. **Sequential Stages**: Stage numbers increment within chapters
3. **Unlock Chain**: unlockCondition references existing stage IDs
4. **Positive Values**: Energy, levels, rewards all > 0
5. **Enemy Positions**: Must be 0-4
6. **Wave Order**: waveNumber increments sequentially
7. **Difficulty Progression**: Harder difficulties have higher energy cost and rewards
8. **Item References**: All itemIds exist in Items.json
9. **Victory Conditions**: type matches BattleRules.json definitions
10. **Star Conditions**: Achievable based on stage design

---

## Stage Design Guidelines

### Difficulty Progression
- **Chapter 1**: Levels 1-10, tutorial difficulty
- **Chapter 2**: Levels 10-20, introducing mechanics
- **Chapter 3-5**: Levels 20-40, standard difficulty
- **Chapter 6-8**: Levels 40-60, challenging
- **Chapter 9-10**: Levels 60-80, endgame

### Reward Scaling
- **Normal Stages**: 100-300 gold repeat
- **Boss Stages**: 300-500 gold repeat
- **First Clear**: 3-5x repeat rewards
- **Gems**: 5-20 per stage (first clear only)

### Energy Efficiency
- **Normal Stages**: 15-20 gold per energy
- **Boss Stages**: 25-35 gold per energy
- **Dungeons**: Specialized resources (50k gold for 15 energy)

---

## Common Use Cases

### Story Progression
1. Player completes Stage 1-1
2. Receives first clear rewards
3. Stage 1-2 unlocks (unlockCondition met)
4. Player levels up from playerExp
5. Continues through chapter to boss

### Dungeon Farming
1. Player checks dungeon availability (daysOfWeek)
2. Selects difficulty based on power
3. Spends energy (energyCost)
4. Receives guaranteed rewards
5. Hits dailyLimit (3/3 runs)
6. Returns next available day

### Star Completion
1. Player clears stage (1 star automatic)
2. Retries for "No Deaths" (earns 2 stars)
3. Optimizes team for speed run (earns 3 stars)
4. Collection shows 3/3 stars
5. Receives all star rewards

---

## Related Configuration Files

- **BattleRules.json**: Combat mechanics, victory conditions
- **Items.json**: All reward items, materials
- **Characters.json**: Enemy character definitions
- **Skills.json**: Enemy abilities
- **AIProfiles.json**: Boss AI behaviors
- **DropTables.json**: Random loot definitions
- **Quests.json**: Stage clear objectives
- **Localization/**: Stage names, descriptions

---

## Version History

- **1.0.0** (2025-11-23): Initial schema documentation
