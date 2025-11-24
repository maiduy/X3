# ArenaConfig.json Schema Documentation

## Overview
This document provides comprehensive descriptions for all indices (fields) in the `ArenaConfig.json` configuration file. This file defines PvP arena modes, ranked ladder systems, matchmaking algorithms, ELO ratings, seasonal rewards, leaderboards, and battle rules for competitive player-versus-player content.

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

### `arenaMode` (object)
- **Type**: Arena Mode Configuration Object
- **Description**: Ranked competitive PvP mode with ELO system
- **Required**: Yes

### `casualMode` (object)
- **Type**: Arena Mode Configuration Object
- **Description**: Casual unranked PvP mode for practice
- **Required**: Yes

---

## Arena Mode Object (Ranked)

### `id` (string)
- **Type**: Unique Identifier
- **Description**: Arena mode identifier
- **Format**: `ARENA_[TYPE]`
- **Example**: `"ARENA_RANKED"`
- **Required**: Yes

---

## Meta Object (Arena Metadata)

### `meta` (object)

#### `meta.nameKey` (string)
- **Type**: Localization Key
- **Description**: Reference key for arena mode's display name
- **Format**: `ARENA_NAME_[TYPE]`
- **Example**: `"ARENA_NAME_RANKED"`

#### `meta.descriptionKey` (string)
- **Type**: Localization Key
- **Description**: Reference key for arena mode description
- **Format**: `ARENA_DESC_[TYPE]`
- **Example**: `"ARENA_DESC_RANKED"`

#### `meta.icon` (string)
- **Type**: Asset Reference
- **Description**: Icon for arena mode in UI
- **Example**: `"Icon_Arena_Ranked"`

---

## Availability Object

### `availability` (object)
Defines unlock requirements for arena mode.

#### `unlockLevel` (number)
- **Type**: Integer
- **Description**: Minimum player level to access this arena mode
- **Range**: 1-100+
- **Example**: `15` (unlocks at level 15)

#### `unlockCondition` (string | null)
- **Type**: Condition Identifier or Null
- **Description**: Additional unlock requirement
- **Values**:
  - `null` - Only level required
  - Condition ID - Specific requirement
- **Example**: `"STAGE_3_BOSS_CLEARED"`
- **Usage**: Progressive unlock system

---

## Matchmaking Object

### `matchmaking` (object)
Defines player matching algorithm and parameters.

#### `algorithm` (string)
- **Type**: Algorithm Type Enum
- **Description**: Matchmaking algorithm used
- **Values**:
  - `"ELO"` - ELO-based skill matching
  - `"PowerBased"` - Team power matching
  - `"Random"` - Random matching
  - `"TierBased"` - Match within same tier only
- **Example**: `"ELO"`
- **Usage**: Determines how opponents are selected

#### `matchmakingRange` (number)
- **Type**: Integer
- **Description**: Maximum ELO/power difference between matched players
- **Default**: `200` for ELO, `500` for power
- **Example**: `200` = match players within ±200 ELO
- **Usage**: Balances queue time vs match quality

#### `maxSearchTime` (number)
- **Type**: Integer (seconds)
- **Description**: Maximum time to search before expanding range
- **Default**: `60`
- **Example**: `60` = expand range after 1 minute
- **Behavior**: Range increases by 50% every maxSearchTime interval

#### `botMatchThreshold` (number)
- **Type**: Integer (seconds)
- **Description**: Time after which bot opponent is offered
- **Default**: `90`
- **Example**: `90` = offer bot match after 1.5 minutes
- **Usage**: Prevents infinite queue times

---

## Seasons Object

### `seasons` (object)
Defines competitive season cycle.

#### `duration` (number)
- **Type**: Integer (days)
- **Description**: Length of competitive season
- **Default**: `30` (1 month)
- **Example**: `30`
- **Usage**: Determines reset frequency

#### `resetDay` (number)
- **Type**: Integer (day of month)
- **Description**: Day when season resets
- **Format**: `1-31`
- **Example**: `1` (first day of month)

#### `preseasonDays` (number)
- **Type**: Integer (days)
- **Description**: Days before season for preparation/announcements
- **Default**: `3`
- **Usage**: Preview rewards, adjust teams

#### `offseasonDays` (number)
- **Type**: Integer (days)
- **Description**: Days after season for reward distribution
- **Default**: `2`
- **Usage**: Calculate final ranks, distribute rewards

**Season Cycle**:
```
Preseason (3 days) → Active Season (30 days) → Offseason (2 days) → New Season
```

---

## Ranking System Object

### `rankingSystem` (object)
Defines rank tiers, divisions, and progression.

#### `initialRank` (string)
- **Type**: Rank Identifier
- **Description**: Starting rank for new players
- **Format**: `{Tier}_{Division}`
- **Example**: `"Bronze_3"`
- **Usage**: Placement after initial matches

#### `tiers` (array)
- **Type**: Array of Tier Objects
- **Description**: All rank tiers in ascending order
- **Required**: Yes

---

## Tier Object

### Tier Configuration

#### `tier` (string)
- **Type**: Tier Name
- **Description**: Name of rank tier
- **Values**: `"Bronze"`, `"Silver"`, `"Gold"`, `"Platinum"`, `"Diamond"`, `"Master"`, `"Grandmaster"`
- **Example**: `"Gold"`

#### `divisions` (number)
- **Type**: Integer
- **Description**: Number of divisions within tier
- **Range**: 1-5
- **Typical Values**:
  - `3` - Standard tiers (Bronze-Diamond)
  - `1` - Elite tiers (Master, Grandmaster)
- **Example**: `3` (Division 3, 2, 1)

#### `eloRange` (object)
- **Type**: ELO Range Object
- **Description**: ELO boundaries for this tier

##### `min` (number)
- **Type**: Integer
- **Description**: Minimum ELO for this tier
- **Example**: `1800` (Gold minimum)

##### `max` (number)
- **Type**: Integer
- **Description**: Maximum ELO for this tier
- **Example**: `2399` (Gold maximum)
- **Note**: Use `999999` for highest tier

#### `divisionThresholds` (array)
- **Type**: Array of Division Threshold Objects
- **Description**: ELO breakpoints for each division

##### Division Threshold Object:

###### `division` (number)
- **Type**: Integer
- **Description**: Division number (3 = lowest, 1 = highest)
- **Example**: `3`, `2`, `1`

###### `elo` (number)
- **Type**: Integer
- **Description**: Minimum ELO for this division
- **Example**: `1800` (Gold III minimum)

**Example**:
```json
"divisionThresholds": [
  {"division": 3, "elo": 1800},  // Gold III: 1800-1999
  {"division": 2, "elo": 2000},  // Gold II:  2000-2199
  {"division": 1, "elo": 2200}   // Gold I:   2200-2399
]
```

#### `icon` (string)
- **Type**: Asset Reference
- **Description**: Rank badge icon
- **Example**: `"Icon_Rank_Gold"`

#### `limitedSlots` (number | omitted)
- **Type**: Integer
- **Description**: Maximum players allowed in this tier
- **Optional**: Only for elite tiers
- **Examples**:
  - `500` - Master tier (top 500 players)
  - `100` - Grandmaster tier (top 100 players)
- **Usage**: Creates competitive prestige tiers

#### `rewards` (object)
- **Type**: Rewards Configuration Object
- **Description**: Rewards for reaching this tier

##### `weekly` (array)
- **Type**: Array of Reward Objects
- **Description**: Rewards given every week in this tier
- **Example**: `[{"itemId": "CURRENCY_ARENA_COIN", "amount": 350}]`

##### `seasonal` (array)
- **Type**: Array of Reward Objects
- **Description**: Rewards given at season end for this tier
- **Example**: Gems, exclusive items, avatars

---

## ELO System Object

### `eloSystem` (object)
ELO rating calculation parameters.

#### `kFactor` (number)
- **Type**: Integer
- **Description**: ELO K-factor for rating changes
- **Default**: `32`
- **Range**: 16-40
- **Usage**: Higher = more volatile ratings
- **Formula**: `ELO_change = K * (actual_score - expected_score)`

#### `placementMatches` (number)
- **Type**: Integer
- **Description**: Number of initial calibration matches
- **Default**: `10`
- **Usage**: Higher K-factor during placement
- **Behavior**: Determines starting rank

#### `maxGainPerMatch` (number)
- **Type**: Integer
- **Description**: Maximum ELO gained from one win
- **Default**: `50`
- **Usage**: Prevents rating inflation

#### `maxLossPerMatch` (number)
- **Type**: Integer
- **Description**: Maximum ELO lost from one loss
- **Default**: `40`
- **Usage**: Softens punishment for losses

#### `winStreakBonus` (object)
- **Type**: Win Streak Configuration
- **Description**: Bonus ELO for consecutive wins

##### `enabled` (boolean)
- **Type**: Boolean
- **Description**: Whether win streak bonuses are active
- **Default**: `true`

##### `bonusPerWin` (number)
- **Type**: Integer
- **Description**: Additional ELO per win in streak
- **Default**: `2`
- **Example**: 3-win streak = +2, +4, +6 bonus

##### `maxBonus` (number)
- **Type**: Integer
- **Description**: Maximum bonus ELO from streak
- **Default**: `10`
- **Usage**: Caps exponential growth

#### `demotionProtection` (object)
- **Type**: Demotion Protection Configuration
- **Description**: Protection from dropping tiers

##### `enabled` (boolean)
- **Type**: Boolean
- **Description**: Whether demotion protection is active
- **Default**: `true`

##### `protectionMatches` (number)
- **Type**: Integer
- **Description**: Number of matches with demotion immunity after promotion
- **Default**: `3`
- **Usage**: Players can't demote for 3 matches after ranking up

##### `appliesToTiers` (array)
- **Type**: Array of Tier Names
- **Description**: Which tiers have demotion protection
- **Example**: `["Silver", "Gold", "Platinum", "Diamond"]`
- **Note**: Usually excludes highest tiers (Master, Grandmaster)

---

## Battle Rules Object (Arena)

### `battleRules` (object)
Arena-specific combat rules.

#### `teamSize` (number)
- **Type**: Integer
- **Description**: Number of characters per team
- **Default**: `5`
- **Range**: 3-5

#### `banPhase` (object)
- **Type**: Ban Phase Configuration
- **Description**: Character ban system

##### `enabled` (boolean)
- **Type**: Boolean
- **Description**: Whether ban phase occurs
- **Default**: `true` for ranked, `false` for casual

##### `bansPerPlayer` (number)
- **Type**: Integer
- **Description**: Number of characters each player can ban
- **Default**: `2`
- **Range**: 1-3

##### `banTime` (number)
- **Type**: Integer (seconds)
- **Description**: Time allowed for ban selection
- **Default**: `30`

**Ban Phase Flow**:
1. Player A bans character
2. Player B bans character
3. Player A bans second character
4. Player B bans second character
5. Proceed to pick phase

#### `pickPhase` (object)
- **Type**: Pick Phase Configuration
- **Description**: Team selection system

##### `enabled` (boolean)
- **Type**: Boolean
- **Description**: Whether pick phase occurs
- **Default**: `true`

##### `pickTime` (number)
- **Type**: Integer (seconds)
- **Description**: Time allowed for team selection
- **Default**: `45` for ranked, `30` for casual

##### `allowDuplicates` (boolean)
- **Type**: Boolean
- **Description**: Whether same character can appear on both teams
- **Default**: `false` for ranked, `true` for casual

#### `turnLimit` (number)
- **Type**: Integer
- **Description**: Maximum turns before draw
- **Default**: `50`
- **Links to**: BattleRules.json

#### `timeLimit` (number)
- **Type**: Integer (seconds)
- **Description**: Maximum battle duration in real-time
- **Default**: `600` (10 minutes)
- **Usage**: Prevents stalling

---

## Rewards Object (Per-Match)

### `rewards` (object)
Per-match reward configuration.

#### `perWin` (array)
- **Type**: Array of Reward Objects
- **Description**: Rewards for winning a match
- **Example**: `[{"itemId": "CURRENCY_ARENA_COIN", "amount": 15}]`

#### `perLoss` (array)
- **Type**: Array of Reward Objects
- **Description**: Consolation rewards for losing
- **Example**: `[{"itemId": "CURRENCY_ARENA_COIN", "amount": 5}]`
- **Usage**: Reduces frustration, encourages participation

#### `firstWinOfDay` (array)
- **Type**: Array of Reward Objects
- **Description**: Bonus for first daily win
- **Example**: `[{"itemId": "CURRENCY_ARENA_COIN", "amount": 50}, {"itemId": "CURRENCY_GEM", "amount": 10}]`
- **Reset**: Daily at midnight
- **Usage**: Encourages daily engagement

---

## Leaderboard Object

### `leaderboard` (object)
Global ranking and rewards.

#### `updateFrequency` (number)
- **Type**: Integer (seconds)
- **Description**: How often leaderboard refreshes
- **Default**: `300` (5 minutes)
- **Usage**: Balance freshness vs server load

#### `displayTopN` (number)
- **Type**: Integer
- **Description**: Number of top players shown
- **Default**: `100`
- **Usage**: Leaderboard UI display limit

#### `topRewards` (array)
- **Type**: Array of Rank Reward Objects
- **Description**: Special rewards for leaderboard positions

##### Rank Reward Object:

###### `rank` (number | omitted)
- **Type**: Integer
- **Description**: Exact rank position
- **Example**: `1` (1st place)
- **Usage**: For specific ranks (1, 2, 3)

###### `rankRange` (object | omitted)
- **Type**: Rank Range Object
- **Description**: Range of ranks for reward
- **Fields**:
  - `min` (number): Lowest rank in range
  - `max` (number): Highest rank in range
- **Example**: `{"min": 4, "max": 10}` (ranks 4-10)

###### `rewards` (array)
- **Type**: Array of Reward Objects
- **Description**: Items awarded to this rank/range
- **Example**: `[{"itemId": "CURRENCY_GEM", "amount": 5000}, {"itemId": "ITEM_EXCLUSIVE_TITLE_RANK1", "amount": 1}]`

**Reward Distribution Example**:
```json
[
  {"rank": 1, "rewards": [...]},           // 1st place only
  {"rank": 2, "rewards": [...]},           // 2nd place only
  {"rank": 3, "rewards": [...]},           // 3rd place only
  {"rankRange": {"min": 4, "max": 10}, "rewards": [...]},   // 4th-10th
  {"rankRange": {"min": 11, "max": 50}, "rewards": [...]},  // 11th-50th
  {"rankRange": {"min": 51, "max": 100}, "rewards": [...]}  // 51st-100th
]
```

---

## Casual Mode Object

### `casualMode` (object)
Unranked practice mode configuration.

**Key Differences from Ranked**:
- **No ELO changes**: `noEloChange: true`
- **Simpler matchmaking**: PowerBased algorithm
- **No ban phase**: `banPhase.enabled: false`
- **Duplicate characters allowed**: `pickPhase.allowDuplicates: true`
- **Lower rewards**: Gold instead of arena coins
- **No tier system**: No ranking/rewards objects
- **Optional daily limit**: Can be unlimited (`dailyLimit: null`)

#### `noEloChange` (boolean)
- **Type**: Boolean
- **Description**: Whether matches affect ELO rating
- **Default**: `true` for casual
- **Usage**: Allows risk-free practice

#### `dailyLimit` (number | null)
- **Type**: Integer or Null
- **Description**: Maximum matches per day
- **Values**:
  - `null` - Unlimited
  - Integer - Specific limit
- **Example**: `null`

---

## ELO Calculation Example

### Match Outcome Calculation

**Given**:
- Player A: ELO 2000, wins against Player B: ELO 2100
- K-factor: 32
- Win streak: 2 wins (bonus: +4)

**Expected Score Formula**:
```
Expected_A = 1 / (1 + 10^((ELO_B - ELO_A) / 400))
Expected_A = 1 / (1 + 10^((2100 - 2000) / 400))
Expected_A = 1 / (1 + 10^0.25) = 1 / 1.778 = 0.36 (36% chance)
```

**ELO Change**:
```
Change = K * (Actual - Expected)
Change = 32 * (1 - 0.36) = 32 * 0.64 = 20.48
Win Streak Bonus = +4
Total Gain = 20 + 4 = 24 ELO
Capped at maxGainPerMatch (50) ✓

Player A: 2000 → 2024 ELO
Player B: 2100 → 2076 ELO (loses ~24)
```

---

## Rank Progression Example

### Player Journey

**Starting**: Bronze III (0 ELO)

1. **Placement Matches** (10 games, 7W-3L)
   - Placed at: Silver II (1400 ELO)

2. **Climb to Gold** (30 games, 18W-12L)
   - Reached: Gold III (1850 ELO)
   - Demotion Protection: Active for 3 matches

3. **Weekly Rewards**: 350 Arena Coins

4. **Season End** (Gold tier)
   - Seasonal Rewards:
     - 2000 Arena Coins
     - 500 Gems
     - 10 Summon Scrolls

---

## Matchmaking Flow

### Queue Process

1. **Enter Queue**: Player searches for match
2. **Initial Range**: ±200 ELO (2000 ± 200 = 1800-2200)
3. **Wait 60s**: maxSearchTime reached
4. **Expand Range**: ±300 ELO (2000 ± 300 = 1700-2300)
5. **Wait 90s** (total): botMatchThreshold reached
6. **Offer Bot Match**: Player can accept bot or continue searching
7. **Match Found**: Opponent at 2050 ELO matched

---

## Data Validation Rules

1. **ELO Ranges**: Tier eloRange.min and max must not overlap
2. **Division Thresholds**: Must be within tier's eloRange
3. **Tier Ordering**: Tiers should be in ascending ELO order
4. **Limited Slots**: Only for top 2-3 tiers
5. **Reward Items**: All itemIds must exist in Items.json
6. **Positive Values**: All ELO, time, and amount values > 0
7. **Division Logic**: Higher division = higher ELO
8. **Season Timing**: preseasonDays + duration + offseasonDays should align with calendar

---

## Rank Design Guidelines

### Tier Distribution (Typical)
- **Bronze**: 20% of players (0-1199 ELO)
- **Silver**: 25% of players (1200-1799 ELO)
- **Gold**: 25% of players (1800-2399 ELO)
- **Platinum**: 15% of players (2400-2999 ELO)
- **Diamond**: 10% of players (3000-3599 ELO)
- **Master**: 4% of players / 500 slots (3600-4199 ELO)
- **Grandmaster**: 1% of players / 100 slots (4200+ ELO)

### Reward Scaling
- **Weekly**: Linear increase per tier
- **Seasonal**: Exponential increase for prestige
- **Exclusive Items**: Only top 2-3 tiers

---

## Common Use Cases

### New Player First Match
1. Player reaches level 15, clears STAGE_3_BOSS
2. Arena unlocks, available in modes list
3. Player queues for first match
4. System starts placement matches (1/10)
5. After 10 matches, ELO calculated, placed in tier

### Rank Promotion
1. Player at Gold III (1999 ELO) wins match
2. Gains 25 ELO → 2024 ELO
3. Crosses threshold (2000 ELO) → Promoted to Gold II
4. Demotion protection activated (3 matches)
5. Next 3 losses won't demote to Gold III

### Season Reset
1. Season ends on day 30
2. Offseason begins (2 days)
3. Calculate final ranks, distribute seasonal rewards
4. Reset all ELO with soft reset: `new_elo = (old_elo + 1200) / 2`
5. Preseason begins (3 days)
6. New season starts, players requeue

---

## Related Configuration Files

- **BattleRules.json**: Combat mechanics used in arena
- **Characters.json**: Character bans reference character IDs
- **Items.json**: All reward items defined here
- **Quests.json**: Arena-related quest objectives
- **Matchmaking.json**: Extended matchmaking configurations
- **Localization/**: Arena names, tier names, descriptions

---

## Version History

- **1.0.0** (2025-11-23): Initial schema documentation
