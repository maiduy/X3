# EventSchedule.json Schema Documentation

## Overview
This document provides comprehensive descriptions for all indices (fields) in the `EventSchedule.json` configuration file. This file defines live operations events including limited-time events, recurring bonuses, seasonal content, login rewards, season passes, event stages, milestones, and global event management rules.

---

## Root Level Fields

### `$schema` (string)
- **Type**: URI
- **Description**: JSON Schema version reference for validation
- **Example**: `"https://json-schema.org/draft/2020-12/schema"`

### `version` (string)
- **Type**: Semantic Version
- **Description**: Configuration file version
- **Format**: `MAJOR.MINOR.PATCH`
- **Example**: `"1.0.0"`

### `lastUpdated` (string)
- **Type**: ISO 8601 DateTime
- **Description**: Timestamp of last configuration update
- **Format**: `YYYY-MM-DDTHH:mm:ssZ`
- **Example**: `"2025-11-23T00:00:00Z"`

### `events` (array)
- **Type**: Array of Event Objects
- **Description**: All scheduled and recurring events
- **Required**: Yes

### `globalEventConfig` (object)
- **Type**: Global Configuration Object
- **Description**: Event system-wide settings
- **Required**: Yes

---

## Event Object Structure

### `id` (string)
- **Type**: Unique Identifier
- **Description**: Event identifier
- **Format**: `EVENT_[NAME]`
- **Examples**:
  - `"EVENT_DOUBLE_DROP"` - Recurring bonus event
  - `"EVENT_DRAGON_RAID"` - Limited-time raid
  - `"EVENT_LOGIN_REWARD"` - Permanent login bonus
  - `"EVENT_SEASON_PASS"` - Monthly season pass
- **Required**: Yes
- **Constraints**: Must be unique across all events

---

## Meta Object (Event Metadata)

### `meta` (object)

#### `meta.nameKey` (string)
- **Type**: Localization Key
- **Description**: Reference key for event's display name
- **Format**: `EVENT_NAME_[EVENT_ID]`
- **Example**: `"EVENT_NAME_DRAGON_RAID"`

#### `meta.descriptionKey` (string)
- **Type**: Localization Key
- **Description**: Reference key for event description
- **Format**: `EVENT_DESC_[EVENT_ID]`
- **Example**: `"EVENT_DESC_DRAGON_RAID"`

#### `meta.icon` (string)
- **Type**: Asset Reference
- **Description**: Event icon sprite
- **Example**: `"Icon_Event_DragonRaid"`

#### `meta.type` (string)
- **Type**: Event Type Enum
- **Description**: Event lifecycle category
- **Values**:
  - `"Permanent"` - Always active (login rewards)
  - `"Recurring"` - Repeats on schedule (weekend bonuses)
  - `"Seasonal"` - Monthly/quarterly cycles (season pass)
  - `"Limited"` - One-time limited duration (special raids)
- **Example**: `"Limited"`
- **Usage**: Determines scheduling behavior

#### `meta.category` (string)
- **Type**: Event Category Enum
- **Description**: Event content type
- **Values**:
  - `"Farming"` - Drop rate bonuses, double EXP
  - `"Raid"` - Boss battles, special stages
  - `"Engagement"` - Login rewards, daily bonuses
  - `"Progression"` - Season pass, battle pass
  - `"Collection"` - Character rate-ups, banners
  - `"Competitive"` - Tournaments, rankings
  - `"Social"` - Guild events, co-op challenges
- **Example**: `"Raid"`
- **Usage**: Event grouping in UI

---

## Schedule Object

### `schedule` (object)
Event timing and recurrence configuration.

#### `startDate` (string)
- **Type**: ISO 8601 DateTime
- **Description**: Event start time (UTC)
- **Format**: `YYYY-MM-DDTHH:mm:ssZ`
- **Example**: `"2025-12-01T00:00:00Z"`
- **Required**: Yes

#### `endDate` (string | null)
- **Type**: ISO 8601 DateTime or Null
- **Description**: Event end time (UTC)
- **Format**: `YYYY-MM-DDTHH:mm:ssZ`
- **Example**: `"2025-12-15T23:59:59Z"`
- **Null**: For permanent events
- **Usage**: Event duration calculation

#### `recurrence` (object | null)
- **Type**: Recurrence Configuration Object
- **Description**: Repeating event schedule
- **Null**: For one-time limited events

##### Recurrence Object:

###### `pattern` (string)
- **Type**: Recurrence Pattern Enum
- **Description**: How often event repeats
- **Values**:
  - `"Daily"` - Every day
  - `"Weekly"` - Every week
  - `"Monthly"` - Every month
  - `"Custom"` - Custom interval
- **Example**: `"Weekly"`

###### `daysOfWeek` (array)
- **Type**: Array of Integers
- **Description**: Days when event is active
- **Format**: `1` = Monday, `2` = Tuesday, ..., `7` = Sunday
- **Example**: `[6, 7]` = Saturday and Sunday
- **Optional**: Only for Weekly pattern

###### `duration` (number)
- **Type**: Integer (hours)
- **Description**: How long each occurrence lasts
- **Example**: `48` = 48 hours (2 days)
- **Usage**: Auto-calculate endDate for recurrences

**Example**:
```json
{
  "pattern": "Weekly",
  "daysOfWeek": [6, 7],
  "duration": 48
}
// = Active every weekend (Sat-Sun) for 48 hours
```

---

## Requirements Object

### `requirements` (object)
Event unlock conditions.

#### `minLevel` (number)
- **Type**: Integer
- **Description**: Minimum player level to participate
- **Example**: `25`
- **Usage**: Level gate for event access

#### `unlockCondition` (string | null)
- **Type**: Condition Identifier or Null
- **Description**: Additional unlock requirement
- **Values**:
  - `null` - No additional requirement
  - Stage ID - Must clear specific stage
  - Achievement ID - Must complete achievement
- **Example**: `"STAGE_5_BOSS_CLEARED"`

---

## Content Object

### `content` (object)
Event-specific content configuration.

**Note**: Content structure varies by event type. Common types include:

---

### Content Type: Raid Event

#### `stages` (array)
Array of event stage configurations.

##### Stage Object:

###### `id` (string)
- **Format**: `EVENT_[EVENT_NAME]_[DIFFICULTY]`
- **Example**: `"EVENT_RAID_EASY"`, `"EVENT_RAID_HARD"`

###### `difficulty` (string)
- **Values**: `"Easy"`, `"Normal"`, `"Hard"`, `"Hell"`, `"Nightmare"`
- **Example**: `"Easy"`

###### `recommendedPower` (number)
- **Type**: Integer
- **Description**: Suggested team power
- **Example**: `3000`

###### `energyCost` (number)
- **Type**: Integer
- **Description**: Energy required to attempt
- **Example**: `15`

###### `boss` (object)
Boss encounter configuration.

- **enemyId** (string): Boss character ID (e.g., `"BOSS_ANCIENT_DRAGON"`)
- **level** (number): Boss level (e.g., `30`)
- **aiProfile** (string): AI behavior pattern (e.g., `"AI_BOSS_PATTERN"`)

###### `rewards` (object)
- **firstClear** (array): One-time first clear rewards
- **repeat** (array): Rewards for each subsequent clear
- **Structure**: `[{"itemId": "...", "amount": number}]`

###### `dailyLimit` (number)
- **Type**: Integer
- **Description**: Maximum runs per day
- **Example**: `5`

#### `eventCurrency` (object)
Special currency for event.

##### `id` (string)
- **Type**: Currency Item ID
- **Description**: Event-specific currency
- **Example**: `"CURRENCY_EVENT_DRAGON_COIN"`

##### `sources` (array)
- **Type**: Array of Source Names
- **Description**: How to earn currency
- **Example**: `["EventStageCompletion", "EventMissions"]`

##### `shop` (string)
- **Type**: Shop ID Reference
- **Description**: Event shop using this currency
- **Example**: `"SHOP_EVENT_DRAGON_RAID"`

#### `milestones` (array)
Point-based progression rewards.

##### Milestone Object:

###### `points` (number)
- **Type**: Integer
- **Description**: Points required to unlock
- **Example**: `1000`, `5000`, `10000`

###### `rewards` (array)
- **Type**: Array of Reward Objects
- **Description**: Items granted at this milestone
- **Example**: `[{"itemId": "CURRENCY_GEM", "amount": 100}]`

---

### Content Type: Login Reward

#### `dailyRewards` (array)
Daily login bonus schedule.

##### Daily Reward Object:

###### `day` (number)
- **Type**: Integer
- **Description**: Login day number
- **Example**: `1`, `2`, `3`, ..., `7`

###### `rewards` (array)
- **Type**: Array of Reward Objects
- **Description**: Items granted for this day
- **Example**: `[{"itemId": "CURRENCY_GOLD", "amount": 10000}]`

#### `resetType` (string)
- **Type**: Reset Pattern Enum
- **Description**: When rewards reset
- **Values**: `"Weekly"`, `"Monthly"`, `"Never"`
- **Example**: `"Weekly"`
- **Usage**: 7-day cycle that repeats

---

### Content Type: Season Pass

#### `tiers` (array)
Season pass level progression.

##### Tier Object:

###### `tier` (number)
- **Type**: Integer
- **Description**: Progression level number
- **Example**: `1`, `10`, `30`, `50`

###### `requiredExp` (number)
- **Type**: Integer
- **Description**: Total experience needed to reach this tier
- **Example**: `0` (tier 1), `5000` (tier 10), `20000` (tier 30)

###### `freeRewards` (array)
- **Type**: Array of Reward Objects
- **Description**: Rewards for free track
- **Example**: `[{"itemId": "CURRENCY_GOLD", "amount": 5000}]`

###### `premiumRewards` (array)
- **Type**: Array of Reward Objects
- **Description**: Additional rewards for premium pass owners
- **Example**: `[{"itemId": "CURRENCY_GEM", "amount": 50}]`

#### `expSources` (array)
Ways to earn season pass experience.

##### EXP Source Object:

###### `activity` (string)
- **Type**: Activity Type Enum
- **Description**: What action grants EXP
- **Values**: `"DailyQuest"`, `"WeeklyQuest"`, `"StageCompletion"`, `"PvPWin"`, `"DungeonClear"`
- **Example**: `"DailyQuest"`

###### `exp` (number)
- **Type**: Integer
- **Description**: Experience points granted
- **Example**: `100`

#### `premiumPass` (object)
Premium upgrade purchase configuration.

##### `price` (array)
- **Type**: Array of Cost Objects
- **Description**: In-game currency cost
- **Example**: `[{"itemId": "CURRENCY_GEM", "amount": 980}]`

##### `iapSku` (string)
- **Type**: IAP Product SKU
- **Description**: In-app purchase product ID
- **Example**: `"com.game.season.pass.premium"`
- **Usage**: Links to app store product

---

## Effects Object

### `effects` (object | null)
Global modifiers applied during event.

#### `globalModifiers` (array)
Array of modifier configurations.

##### Modifier Object:

###### `type` (string)
- **Type**: Modifier Type Enum
- **Description**: What type of bonus
- **Values**:
  - `"DropRateMultiplier"` - Increase drop rates
  - `"ExpMultiplier"` - Increase EXP gain
  - `"GoldMultiplier"` - Increase gold gain
  - `"EnergyRegenBoost"` - Faster energy regen
  - `"StageRewardBonus"` - Bonus stage rewards
- **Example**: `"DropRateMultiplier"`

###### `target` (string)
- **Type**: Target Scope Enum
- **Description**: What content is affected
- **Values**: `"AllStages"`, `"Dungeons"`, `"ArenaMatches"`, `"SpecificStage"`
- **Example**: `"AllStages"`

###### `multiplier` (number)
- **Type**: Float
- **Description**: Bonus multiplier
- **Example**: `2.0` (double rewards)
- **Format**: `1.0` = normal, `2.0` = 2x, `1.5` = 1.5x

**Null**: For events without global effects (raid events, login rewards)

---

## Rewards Field

### `rewards` (array | null)
Direct rewards for event participation (not from stages).

- **Type**: Array of Reward Objects
- **Structure**: `[{"itemId": "...", "amount": number}]`
- **Null**: For events with stage-based or tier-based rewards
- **Usage**: Simple participation rewards

---

## Announcement Object

### `announcement` (object)
Event notification configuration.

#### `showInGame` (boolean)
- **Type**: Boolean
- **Description**: Show event in game event list
- **Default**: `true`

#### `showPopup` (boolean)
- **Type**: Boolean
- **Description**: Show popup notification when event starts
- **Default**: `true` for major events, `false` for minor

#### `notifyBeforeStart` (number)
- **Type**: Integer (seconds)
- **Description**: Notify players this long before event starts
- **Examples**:
  - `86400` = 24 hours (1 day)
  - `172800` = 48 hours (2 days)
  - `259200` = 72 hours (3 days)
  - `0` = No advance notification

---

## Global Event Config Object

### `globalEventConfig` (object)
System-wide event management settings.

#### `maxConcurrentEvents` (number)
- **Type**: Integer
- **Description**: Maximum active events at once
- **Default**: `5`
- **Usage**: Prevent event overload

#### `priorityOrder` (array)
- **Type**: Array of Event Type Names
- **Description**: Priority for overlapping events
- **Example**: `["Limited", "Seasonal", "Recurring", "Permanent"]`
- **Usage**: Higher priority events shown first in UI

#### `conflictResolution` (string)
- **Type**: Resolution Strategy Enum
- **Description**: How to handle event conflicts
- **Values**:
  - `"Priority"` - Use priorityOrder
  - `"Newest"` - Newest event takes precedence
  - `"Oldest"` - Oldest event takes precedence
- **Default**: `"Priority"`

#### `notificationSettings` (object)
Global notification preferences.

##### `pushNotifications` (boolean)
- **Type**: Boolean
- **Description**: Enable OS push notifications for events
- **Default**: `true`

##### `inGamePopups` (boolean)
- **Type**: Boolean
- **Description**: Enable in-game event popups
- **Default**: `true`

##### `mailboxMessages` (boolean)
- **Type**: Boolean
- **Description**: Send event notifications to in-game mailbox
- **Default**: `true`

---

## Event Type Patterns

### Recurring Event (Weekend Bonuses)
```json
{
  "type": "Recurring",
  "schedule": {
    "startDate": "2025-11-25T00:00:00Z",
    "endDate": "2025-11-27T23:59:59Z",
    "recurrence": {
      "pattern": "Weekly",
      "daysOfWeek": [6, 7],
      "duration": 48
    }
  },
  "effects": {
    "globalModifiers": [
      {"type": "DropRateMultiplier", "target": "AllStages", "multiplier": 2.0}
    ]
  }
}
// Active every Saturday-Sunday, doubles all drop rates
```

### Limited Event (Special Raid)
```json
{
  "type": "Limited",
  "schedule": {
    "startDate": "2025-12-01T00:00:00Z",
    "endDate": "2025-12-15T23:59:59Z",
    "recurrence": null
  },
  "content": {
    "stages": [...],
    "eventCurrency": {...},
    "milestones": [...]
  }
}
// Runs Dec 1-15 only, has special stages and progression
```

### Permanent Event (Daily Login)
```json
{
  "type": "Permanent",
  "schedule": {
    "startDate": "2025-01-01T00:00:00Z",
    "endDate": null,
    "recurrence": null
  },
  "content": {
    "dailyRewards": [...],
    "resetType": "Weekly"
  }
}
// Always active, rewards reset weekly
```

### Seasonal Event (Monthly Pass)
```json
{
  "type": "Seasonal",
  "schedule": {
    "startDate": "2025-11-01T00:00:00Z",
    "endDate": "2025-11-30T23:59:59Z",
    "recurrence": {
      "pattern": "Monthly",
      "duration": 30
    }
  },
  "content": {
    "tiers": [...],
    "premiumPass": {...}
  }
}
// 30-day cycle, repeats monthly
```

---

## Event Scheduling Examples

### Recurrence Calculation

**Weekly Event**:
```
Pattern: Weekly
Days: [6, 7] (Sat, Sun)
Duration: 48 hours

First Occurrence: Nov 25 00:00 - Nov 27 23:59
Next Occurrence: Dec 2 00:00 - Dec 4 23:59
Repeats: Every weekend
```

**Monthly Season Pass**:
```
Pattern: Monthly
Duration: 30 days

Season 1: Nov 1 - Nov 30
Season 2: Dec 1 - Dec 31
Season 3: Jan 1 - Jan 31
```

---

## Season Pass Progression Example

### Tier Progression
```
Tier 1:  0 EXP     → Rewards: 5k gold (free), 10k gold + 50 gems (premium)
Tier 10: 5,000 EXP → Rewards: 1 scroll (free), 5 scrolls + 100 gems (premium)
Tier 30: 20,000 EXP → Rewards: 5 rare ore (free), 50 char fragments + exclusive skin (premium)
```

### EXP Sources
```
Daily Quest: 100 EXP
Weekly Quest: 500 EXP
Stage Clear: 10 EXP

To reach Tier 30 (20,000 EXP):
- 200 daily quests OR
- 40 weekly quests OR
- 2000 stage clears
- Typically: Mix of all activities over 30 days
```

---

## Milestone Progression Example

### Dragon Raid Milestones
```
1,000 points:  100 gems
5,000 points:  5 scrolls + 10 dragon scales
10,000 points: 50 Fire Dragon fragments + exclusive title

Point Sources:
- Easy Raid Clear: 20 points
- Hard Raid Clear: 50 points
- Event Missions: 100-500 points each

To reach 10,000 points:
- 200 Easy clears OR
- 80 Hard clears OR
- 10-20 event missions
```

---

## Global Modifier Application

### Double Drop Weekend
```
Base Drop Rate: 30%
Event Modifier: 2.0x
Effective Drop Rate: 60%

Player farms Stage 1-5:
- Normal: 30% chance for rare ore
- During Event: 60% chance for rare ore
- Twice as likely to get drops
```

---

## Data Validation Rules

1. **Unique IDs**: All event IDs must be unique
2. **Date Logic**: endDate must be after startDate (if not null)
3. **Recurrence Validity**: Weekly pattern requires daysOfWeek array
4. **Duration**: Recurring events need duration field
5. **Content Type**: Raid events need stages array, Season pass needs tiers array
6. **Reward References**: All itemIds must exist in Items.json
7. **Level Requirements**: minLevel should be reasonable (1-80)
8. **Priority Order**: All event types in priorityOrder should be valid
9. **Notification Timing**: notifyBeforeStart should be reasonable (0-604800 seconds/7 days)
10. **Multiplier Validity**: All multipliers should be > 0

---

## Event Design Guidelines

### Limited Events
- **Duration**: 7-14 days typical
- **Frequency**: 1-2 per month
- **Rewards**: Exclusive characters, rare materials
- **Milestones**: 3-5 major milestones
- **Daily Limits**: 3-10 runs per difficulty

### Recurring Events
- **Pattern**: Weekly (weekends most common)
- **Duration**: 24-72 hours
- **Multipliers**: 1.5x - 3.0x
- **Purpose**: Encourage regular engagement

### Seasonal Events
- **Duration**: 30-90 days
- **Tiers**: 30-50 levels typical
- **Premium Cost**: 800-1200 gems
- **Value**: 5-10x premium cost in rewards

### Permanent Events
- **Reset**: Daily, Weekly, or Monthly
- **Rewards**: Modest but consistent
- **Purpose**: Retention and daily login habit

---

## Common Use Cases

### Event Activation Check
```
Current Time: 2025-12-05 12:00:00 UTC
Event: Dragon Raid
- startDate: 2025-12-01 00:00:00 UTC
- endDate: 2025-12-15 23:59:59 UTC
- Is Active: Yes (within date range)
- Player Level: 30 (≥ minLevel 25)
- Unlock: STAGE_5_BOSS cleared
- Result: Event available to player
```

### Milestone Progress
```
Player Points: 4500
Next Milestone: 5000 points (500 more needed)
Rewards Preview: 5 scrolls + 10 dragon scales

To reach:
- 10 more Easy raids (50 points each) OR
- 5 more Hard raids (100 points each) OR
- 1-2 event missions (250-500 points each)
```

### Season Pass Purchase
```
Player at Tier 15 (free track)
Purchases Premium Pass (980 gems)
Instantly Unlocks:
- Tier 1-15 premium rewards (retroactive)
- Access to premium track for Tier 16-30
- Total Value: ~5000 gems worth of items
```

---

## Related Configuration Files

- **Quests.json**: Event quests and missions
- **Items.json**: Event currency and reward items
- **Levels.json**: Event stage configurations
- **Rewards.json**: Milestone and login reward structures
- **ShopProducts.json**: Event shops and premium pass IAP
- **FeatureFlags.json**: Can enable/disable events
- **Localization/**: Event names and descriptions

---

## Version History

- **1.0.0** (2025-11-23): Initial schema documentation
