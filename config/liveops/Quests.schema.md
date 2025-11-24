# Quests.json Schema Documentation

## Overview
This document provides comprehensive descriptions for all indices (fields) in the `Quests.json` configuration file. This file defines daily quests, weekly quests, story missions, event quests, objectives, progression tracking, and reward systems.

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

### `questCategories` (object)
- **Type**: Quest Category Configuration Object
- **Description**: Defines different quest types and their reset schedules
- **Required**: Yes

### `quests` (array)
- **Type**: Array of Quest Objects
- **Description**: Complete list of all quests in the game
- **Required**: Yes

---

## Quest Categories Object

### `questCategories` (object)
Configuration for different quest types and their reset behaviors.

---

### Daily Quest Category

#### `daily` (object)

##### `resetTime` (string)
- **Type**: Time String
- **Description**: Time of day when daily quests reset
- **Format**: `"HH:MM:SS"` (24-hour)
- **Example**: `"00:00:00"` (midnight)
- **Usage**: All daily quests reset at this time

##### `resetTimezone` (string)
- **Type**: Timezone String
- **Description**: Timezone for reset time
- **Format**: IANA timezone or "UTC"
- **Example**: `"UTC"`, `"America/New_York"`, `"Asia/Tokyo"`
- **Usage**: Determines when midnight occurs globally

##### `maxActive` (number)
- **Type**: Integer
- **Description**: Maximum number of active daily quests simultaneously
- **Default**: `10`
- **Usage**: Quest list capacity, UI pagination

---

### Weekly Quest Category

#### `weekly` (object)

##### `resetDay` (number)
- **Type**: Integer (day of week)
- **Description**: Day of week when weekly quests reset
- **Format**: `0` = Sunday, `1` = Monday, ... `6` = Saturday
- **Example**: `1` (Monday)
- **Usage**: Weekly content cadence

##### `resetTime` (string)
- **Type**: Time String
- **Description**: Time of day when weekly quests reset
- **Format**: `"HH:MM:SS"` (24-hour)
- **Example**: `"00:00:00"`

##### `maxActive` (number)
- **Type**: Integer
- **Description**: Maximum number of active weekly quests
- **Default**: `10`

---

### Story Quest Category

#### `story` (object)

##### `persistent` (boolean)
- **Type**: Boolean
- **Description**: Whether story quests persist across sessions
- **Default**: `true`
- **Usage**: Story progress is permanent, no reset

**Note**: Story quests typically have no reset mechanism as they represent permanent progression.

---

### Event Quest Category

#### `event` (object)

##### `linkedToEvents` (boolean)
- **Type**: Boolean
- **Description**: Whether event quests are tied to active events
- **Default**: `true`
- **Usage**: Event quests appear/disappear with events
- **Behavior**: Reset/availability controlled by EventSchedule.json

---

## Quest Object Structure

### `id` (string)
- **Type**: Unique Identifier
- **Description**: Immutable quest identifier
- **Format**: `[CATEGORY]_[NAME]`
- **Examples**:
  - `"DAILY_COMPLETE_STAGES"` - Daily quest
  - `"WEEKLY_ENHANCE_ITEMS"` - Weekly quest
  - `"STORY_CHAPTER_1"` - Story quest
  - `"EVENT_DRAGON_HUNT"` - Event quest
- **Required**: Yes
- **Constraints**: Must be unique across all quests

---

## Meta Object (Quest Metadata)

### `meta` (object)
Contains display and categorization metadata for the quest.

#### `meta.nameKey` (string)
- **Type**: Localization Key
- **Description**: Reference key for quest's display name
- **Format**: `QUEST_NAME_[QUEST_ID]`
- **Example**: `"QUEST_NAME_COMPLETE_STAGES"`
- **Usage**: Quest list title

#### `meta.descriptionKey` (string)
- **Type**: Localization Key
- **Description**: Reference key for quest's description text
- **Format**: `QUEST_DESC_[QUEST_ID]`
- **Example**: `"QUEST_DESC_COMPLETE_STAGES"`
- **Usage**: Quest details, tooltip

#### `meta.icon` (string)
- **Type**: Asset Reference
- **Description**: Icon sprite for quest in UI
- **Example**: `"Icon_Quest_Stage"`
- **Usage**: Quest list visual identification

#### `meta.category` (string)
- **Type**: Category Enum
- **Description**: Quest type category
- **Values**: `"daily"`, `"weekly"`, `"story"`, `"event"`, `"achievement"`
- **Example**: `"daily"`
- **Usage**: Determines reset behavior, UI organization

#### `meta.displayOrder` (number)
- **Type**: Integer
- **Description**: Sort order in quest list
- **Range**: 1+
- **Example**: `1` (appears first)
- **Usage**: UI ordering within category

---

## Requirements Object

### `requirements` (object)
Defines unlock conditions for the quest.

#### `minLevel` (number)
- **Type**: Integer
- **Description**: Minimum player level to access quest
- **Range**: 1-100+
- **Example**: `15`
- **Usage**: Level-gates advanced content

#### `unlockCondition` (string | null)
- **Type**: Condition Identifier or Null
- **Description**: Additional requirement beyond level
- **Values**:
  - `null` - No additional requirement
  - `"ARENA_UNLOCKED"` - Feature unlock
  - `"STORY_CHAPTER_1_COMPLETE"` - Progression gate
  - `"VIP_LEVEL_3"` - Monetization gate
- **Example**: `"ARENA_UNLOCKED"`
- **Usage**: Progressive content unlocking

---

## Objectives Array

### `objectives` (array)
- **Type**: Array of Objective Objects
- **Description**: Tasks player must complete to finish quest
- **Required**: Yes, must have at least one objective

### Objective Object

#### `id` (string)
- **Type**: Unique Identifier
- **Description**: Objective identifier (unique within quest)
- **Format**: `OBJ_[DESCRIPTION]`
- **Example**: `"OBJ_COMPLETE_STAGES"`

#### `type` (string)
- **Type**: Objective Type Enum
- **Description**: Category of objective
- **Values**:
  - `"StageCompletion"` - Complete stages/levels
  - `"PvPVictory"` - Win PvP battles
  - `"ItemEnhancement"` - Enhance equipment
  - `"EnemyKill"` - Defeat enemies
  - `"SpecificStage"` - Complete specific stage
  - `"ResourceCollection"` - Collect resources
  - `"CharacterAscension"` - Ascend characters
  - `"SocialInteraction"` - Friend actions
- **Example**: `"StageCompletion"`

#### `listeners` (array)
- **Type**: Array of Event Listener Objects
- **Description**: Events that trigger objective progress
- **Required**: Yes

##### Event Listener Object

###### `event` (string)
- **Type**: Event Name Enum
- **Description**: Game event that updates progress
- **Common Events**:
  - `"OnStageComplete"` - Stage cleared
  - `"OnPvPVictory"` - PvP battle won
  - `"OnItemEnhance"` - Equipment upgraded
  - `"OnEnemyKilled"` - Enemy defeated
  - `"OnCharacterAscend"` - Character ascended
  - `"OnResourceGain"` - Resource collected
  - `"OnFriendAdd"` - Friend added
- **Example**: `"OnStageComplete"`
- **Usage**: System triggers when this event occurs

###### `filters` (object)
- **Type**: Filter Criteria Object
- **Description**: Conditions that must be met for event to count
- **Structure**: Varies by event type

**Common Filter Fields**:

- **stageType** (string): `"Any"`, `"Story"`, `"Event"`, `"Daily"`
- **stageId** (string): Specific stage ID (e.g., `"STAGE_1_BOSS"`)
- **mode** (string): `"Arena"`, `"RankedPvP"`, `"GuildWar"`
- **itemType** (string): `"Weapon"`, `"Armor"`, `"Any"`
- **itemRarity** (string): `"SSR"`, `"SR"`, `"R"`
- **tag** (string): Enemy tag (e.g., `"Boss"`, `"Elite"`)
- **characterId** (string): Specific character ID
- **element** (string): Elemental type
- **difficulty** (string): `"Normal"`, `"Hard"`, `"Hell"`

**Filter Examples**:
```json
{"stageType": "Any"} // Any stage counts
{"mode": "Arena"} // Only Arena battles
{"tag": "Boss"} // Only boss kills
{"itemType": "Weapon", "itemRarity": "SSR"} // SSR weapons only
```

###### `requiredCount` (number)
- **Type**: Integer
- **Description**: Number of times event must occur
- **Range**: 1+
- **Example**: `5` (complete 5 stages)
- **Usage**: Objective completion threshold

#### `descriptionKey` (string)
- **Type**: Localization Key
- **Description**: Player-facing objective text
- **Format**: `OBJ_DESC_[DESCRIPTION]`
- **Example**: `"OBJ_DESC_COMPLETE_5_STAGES"`
- **Usage**: Progress display (e.g., "Complete 5 Stages: 3/5")

---

## Rewards Array

### `rewards` (array)
- **Type**: Array of Reward Objects
- **Description**: Items granted upon quest completion
- **Required**: Yes, typically at least one reward

### Reward Object

#### `itemId` (string)
- **Type**: Item Identifier
- **Description**: ID of reward item
- **Format**: References Items.json
- **Examples**:
  - `"CURRENCY_GOLD"` - Gold currency
  - `"CURRENCY_GEM"` - Premium currency
  - `"RESOURCE_ENERGY"` - Stamina resource
  - `"MAT_WEAPON_ORE"` - Material
  - `"CHAR_SHADOW_ASSASSIN_FRAGMENT"` - Character fragment
- **Required**: Yes

#### `amount` (number)
- **Type**: Integer
- **Description**: Quantity of item awarded
- **Range**: 1+
- **Example**: `5000` (5000 gold)
- **Required**: Yes

**Example Rewards**:
```json
[
  {"itemId": "CURRENCY_GOLD", "amount": 5000},
  {"itemId": "RESOURCE_ENERGY", "amount": 20},
  {"itemId": "CURRENCY_GEM", "amount": 10}
]
```

---

## Chain Quest Field

### `chainQuest` (string | null)
- **Type**: Quest ID Reference or Null
- **Description**: Next quest in chain that unlocks upon completion
- **Values**:
  - `null` - No follow-up quest
  - Quest ID string - Unlocks specified quest
- **Example**: `"STORY_CHAPTER_2"`
- **Usage**: Story progression, quest chains

**Chain Quest Behavior**:
1. Player completes `STORY_CHAPTER_1`
2. Receives rewards
3. `STORY_CHAPTER_2` becomes available
4. Continues quest chain

---

## Quest Type Patterns

### Daily Quests
- **Category**: `"daily"`
- **Reset**: Daily at configured time
- **Objectives**: Usually simple, repeatable tasks
- **Rewards**: Moderate (gold, energy, basic materials)
- **Examples**:
  - Complete 5 stages
  - Win 3 Arena battles
  - Enhance 10 items
  - Spend 500 energy

### Weekly Quests
- **Category**: `"weekly"`
- **Reset**: Weekly on configured day
- **Objectives**: More challenging, higher counts
- **Rewards**: Substantial (gems, rare materials, tickets)
- **Examples**:
  - Enhance 20 items
  - Defeat 10 bosses
  - Win 20 PvP battles
  - Complete 30 stages

### Story Quests
- **Category**: `"story"`
- **Reset**: Never (permanent progression)
- **Objectives**: Specific story stages
- **Rewards**: Large (gems, character unlocks, story items)
- **Chain**: Often links to next chapter
- **Examples**:
  - Complete Chapter 1 Boss
  - Complete Chapter 2 Boss
  - Unlock specific character

### Event Quests
- **Category**: `"event"`
- **Reset**: Tied to event schedule
- **Objectives**: Event-specific activities
- **Rewards**: Event currency, exclusive items
- **Examples**:
  - Complete event stages
  - Defeat event boss
  - Collect event tokens

---

## Objective Tracking System

### Progress Calculation

**Given**: Quest "Complete 5 Stages"
1. Player completes a stage
2. `OnStageComplete` event fires
3. Check listener filters: `{"stageType": "Any"}` → Passes
4. Increment progress: `3/5` → `4/5`
5. If progress = requiredCount (5), objective complete
6. If all objectives complete, quest complete → Grant rewards

### Multiple Objectives

Quests can have multiple objectives (AND logic):
```json
"objectives": [
  {
    "id": "OBJ_WIN_5_BATTLES",
    "type": "PvPVictory",
    "listeners": [{"event": "OnPvPVictory", "filters": {}, "requiredCount": 5}]
  },
  {
    "id": "OBJ_KILL_10_ENEMIES",
    "type": "EnemyKill",
    "listeners": [{"event": "OnEnemyKilled", "filters": {}, "requiredCount": 10}]
  }
]
```
**Completion**: Both objectives must reach requiredCount.

---

## Quest State Machine

### States
1. **Locked** - Requirements not met (level/unlock condition)
2. **Available** - Can be accepted
3. **Active** - Player has accepted, tracking progress
4. **Completed** - All objectives done, rewards claimable
5. **Claimed** - Rewards collected, quest finished
6. **Expired** - Time-limited quest expired (daily/weekly/event)

### State Transitions
```
Locked → Available (when requirements met)
Available → Active (player accepts)
Active → Completed (objectives done)
Completed → Claimed (rewards collected)
Active → Expired (time limit reached)
```

---

## Quest UI Information

### Quest List Display
For each quest, display:
- Icon (from `meta.icon`)
- Name (from `meta.nameKey` localization)
- Category badge (daily/weekly/story/event)
- Progress: "3/5 Objectives Complete"
- Rewards preview
- Accept/Claim button state

### Quest Details Panel
When selected:
- Full description (from `meta.descriptionKey`)
- All objectives with progress bars
- Detailed rewards list
- Chain quest indicator (if applicable)
- Time remaining (for daily/weekly)

---

## Data Validation Rules

1. **Unique IDs**: All quest IDs and objective IDs must be unique
2. **Category Match**: Quest category must exist in questCategories
3. **Item References**: All reward itemIds must exist in Items.json
4. **Chain References**: chainQuest IDs must exist in quests array
5. **Unlock Conditions**: unlockCondition strings should match game features
6. **Filter Validity**: Filter fields should match event type expectations
7. **Positive Counts**: requiredCount and amount must be > 0
8. **Level Range**: minLevel should be within game level cap

---

## Quest Design Guidelines

### Daily Quest Rewards
- **Gold**: 5,000 - 20,000
- **Energy**: 10 - 50
- **Gems**: 5 - 20
- **Materials**: Common tier, small quantities

### Weekly Quest Rewards
- **Gold**: 50,000 - 200,000
- **Gems**: 50 - 200
- **Materials**: Rare tier, moderate quantities
- **Tickets**: Summon tickets, PvP tickets

### Story Quest Rewards
- **Gems**: 50 - 500
- **Character Fragments**: 10 - 50
- **Unique Items**: Story-specific equipment
- **Currency**: Large gold/gem bonuses

### Objective Difficulty Scaling
- **Level 1-10**: 3-5 completions
- **Level 11-30**: 5-10 completions
- **Level 31-50**: 10-20 completions
- **Level 51+**: 20-50 completions

---

## Common Use Cases

### Daily Quest Reset
1. Server detects `resetTime` reached in `resetTimezone`
2. Mark all daily quests as Expired (if active)
3. Reset progress counters to 0
4. Set all daily quests to Available state
5. Notify players of new daily quests

### Quest Progress Update
1. Player completes a stage
2. System broadcasts `OnStageComplete` event
3. All active quests check their objectives
4. Objectives with `OnStageComplete` listener check filters
5. Matching objectives increment progress
6. If objective reaches requiredCount, mark complete
7. If all quest objectives complete, mark quest complete

### Chain Quest Unlock
1. Player claims rewards for `STORY_CHAPTER_1`
2. System checks `chainQuest` field: `"STORY_CHAPTER_2"`
3. Set `STORY_CHAPTER_2` to Available state
4. Notify player of new quest unlocked
5. Display quest in story quest list

---

## Related Configuration Files

- **Items.json**: Reward items defined here
- **EventSchedule.json**: Controls event quest availability
- **Levels.json**: Stage IDs referenced in specific stage objectives
- **Characters.json**: Character IDs in ascension/collection quests
- **ArenaConfig.json**: PvP modes referenced in filters
- **Localization/**: Quest names, descriptions, objective text

---

## Version History

- **1.0.0** (2025-11-23): Initial schema documentation
