# Matchmaking.json Schema Documentation

## Overview
Defines matchmaking algorithms, parameters, queue priorities, bot matching rules, anti-smurfing detection, and fairness systems for PvP and Co-op modes.

---

## Root Level Fields

### `rankedMatchmaking` (object)
Competitive ranked PvP matchmaking configuration.

### `casualMatchmaking` (object)
Unranked casual PvP matchmaking configuration.

### `coopMatchmaking` (object)
Co-op PvE matchmaking configuration.

### `antiSmurfing` (object)
Smurf account detection and handling.

### `fairnessRules` (object)
Matchmaking fairness and balance rules.

### `backfillSystem` (object)
Player replacement for disconnected players.

---

## Ranked Matchmaking Object

### `algorithm` (string)
- **Type**: Algorithm Enum
- **Values**: `"ELO"`, `"MMR"`, `"TrueSkill"`, `"Glicko"`
- **Default**: `"ELO"`
- **Description**: Skill rating system used

### `parameters` (object)

#### `matchmakingRange` (object)
Initial and expanding ELO range for matches.

##### `initial` (number)
- **Type**: Integer (ELO points)
- **Description**: Starting search range
- **Default**: `100` (±100 ELO)
- **Example**: Player at 2000 ELO searches 1900-2100

##### `expansionRate` (number)
- **Type**: Integer (ELO points)
- **Description**: Range increase per expansion interval
- **Default**: `50`
- **Example**: After 10s: ±150, after 20s: ±200

##### `maxRange` (number)
- **Type**: Integer (ELO points)
- **Description**: Maximum allowed range expansion
- **Default**: `500`
- **Usage**: Prevents extremely unfair matches

##### `expansionInterval` (number)
- **Type**: Integer (seconds)
- **Description**: Time between range expansions
- **Default**: `10`

#### `eloConsideration` (object)
Weight of ELO in matchmaking algorithm.

##### `weight` (number)
- **Type**: Float (0.0-1.0)
- **Description**: ELO importance in match quality
- **Default**: `0.7` (70%)

##### `rangeMultiplier` (number)
- **Type**: Float
- **Description**: Multiplier for ELO range
- **Default**: `1.0`

#### `powerConsideration` (object)
Team power factor in matchmaking.

##### `weight` (number)
- **Type**: Float (0.0-1.0)
- **Description**: Power importance
- **Default**: `0.2` (20%)

##### `maxDifference` (number)
- **Type**: Integer
- **Description**: Maximum allowed power gap
- **Example**: `2000` power difference

#### `winStreakConsideration` (object)
Win streak influence on matchmaking.

##### `weight` (number)
- **Type**: Float (0.0-1.0)
- **Description**: Streak importance
- **Default**: `0.1` (10%)

##### `bonusEloPerWin` (number)
- **Type**: Integer
- **Description**: Virtual ELO boost per consecutive win
- **Example**: `10` (3 win streak = +30 virtual ELO)

### `queuePriority` (object)

#### `waitTimeBonus` (object)
Priority increase for long queue times.

##### `enabled` (boolean)
- **Default**: `true`

##### `bonusPerSecond` (number)
- **Type**: Float
- **Description**: Priority increase per second waited
- **Default**: `0.5`

##### `maxBonus` (number)
- **Type**: Integer
- **Description**: Maximum priority bonus
- **Default**: `100`

#### `tierProtection` (object)
Prevent cross-tier matching.

##### `enabled` (boolean)
- **Default**: `true`

##### `preventCrossTier` (array)
- **Type**: Array of Tier Names
- **Description**: Tiers that can't match outside their tier
- **Example**: `["Master", "Grandmaster"]`
- **Usage**: Protect elite players from lower tiers

### `timeouts` (object)

#### `maxSearchTime` (number)
- **Type**: Integer (seconds)
- **Description**: Maximum matchmaking duration
- **Default**: `120` (2 minutes)

#### `botMatchThreshold` (number)
- **Type**: Integer (seconds)
- **Description**: When to offer bot match
- **Default**: `90` (1.5 minutes)

#### `requeueDelay` (number)
- **Type**: Integer (seconds)
- **Description**: Cooldown before requeuing
- **Default**: `5`

### `botMatching` (object)

#### `enabled` (boolean)
- **Description**: Allow bot opponents
- **Default**: `true`

#### `botEloRange` (number)
- **Type**: Integer
- **Description**: Bot ELO range from player
- **Example**: `50` (bots within ±50 ELO)

#### `preferRealPlayers` (boolean)
- **Description**: Prioritize human opponents
- **Default**: `true`

#### `botDifficultyScaling` (boolean)
- **Description**: Scale bot skill to player level
- **Default**: `true`

---

## Casual Matchmaking Object

### `algorithm` (string)
- **Values**: `"PowerBased"`, `"LevelBased"`, `"Random"`
- **Default**: `"PowerBased"`

### `parameters` (object)

#### `powerRange` (object)
Team power matching ranges.

##### `initial` (number)
- **Default**: `500` (±500 power)

##### `expansionRate` (number)
- **Default**: `200`

##### `maxRange` (number)
- **Default**: `2000`

##### `expansionInterval` (number)
- **Default**: `15` seconds

#### `levelConsideration` (object)

##### `weight` (number)
- **Default**: `0.3` (30%)

##### `maxDifference` (number)
- **Type**: Integer (levels)
- **Description**: Maximum level gap
- **Example**: `10` levels

### `timeouts` (object)

#### `maxSearchTime` (number)
- **Default**: `60` seconds (faster than ranked)

#### `botMatchThreshold` (number)
- **Default**: `45` seconds (quicker bot matches)

#### `requeueDelay` (number)
- **Default**: `3` seconds

### `botMatching` (object)

#### `botPowerRange` (number)
- **Default**: `300`

#### `preferRealPlayers` (boolean)
- **Default**: `false` (accepts bots quickly)

---

## Co-op Matchmaking Object

### `algorithm` (string)
- **Default**: `"PartyBased"`

### `parameters` (object)

#### `partySize` (object)

##### `min` (number)
- **Default**: `1` (solo allowed)

##### `max` (number)
- **Default**: `4` (4-player co-op)

#### `averagePowerMatching` (boolean)
- **Description**: Match by party average power
- **Default**: `true`

#### `roleBalancing` (object)
Ensure balanced team composition.

##### `enabled` (boolean)
- **Default**: `true`

##### `preferredComposition` (object)
- **Description**: Ideal team roles
- **Example**:
```json
{
  "Tank": 1,
  "DPS": 2,
  "Support": 1
}
```

### `timeouts` (object)

#### `maxSearchTime` (number)
- **Default**: `90` seconds

#### `fillWithBots` (boolean)
- **Description**: Fill empty slots with bots
- **Default**: `true`

#### `requeueDelay` (number)
- **Default**: `5` seconds

---

## Anti-Smurfing Object

### `enabled` (boolean)
- **Description**: Smurf detection active
- **Default**: `true`

### `newAccountDetection` (object)

#### `winRateThreshold` (number)
- **Type**: Float (0.0-1.0)
- **Description**: Suspicious win rate
- **Example**: `0.8` (80%+ wins suggests smurf)

#### `gamesPlayed` (number)
- **Type**: Integer
- **Description**: Sample size for detection
- **Default**: `20` games

#### `actionOnDetection` (string)
- **Values**:
  - `"AccelerateEloGain"` - Gain more ELO per win
  - `"HigherMMRMatches"` - Match against stronger opponents
  - `"FlagForReview"` - Manual review
- **Default**: `"AccelerateEloGain"`

### `eloBoostDetection` (object)

#### `enabled` (boolean)
- **Default**: `true`

#### `suspiciousPatterns` (array)
- **Type**: Array of Pattern IDs
- **Values**: `["RapidEloGain", "PartyMMRGap", "WinTradingPattern"]`
- **Description**: Patterns indicating boosting

#### `actionOnDetection` (string)
- **Values**: `"FlagForReview"`, `"TemporaryBan"`, `"EloRollback"`
- **Default**: `"FlagForReview"`

---

## Fairness Rules Object

### `maxPartyMMRDifference` (number)
- **Type**: Integer (MMR/ELO points)
- **Description**: Maximum rating gap within party
- **Default**: `400`
- **Usage**: Prevent boosting

### `soloQueueProtection` (object)

#### `enabled` (boolean)
- **Description**: Protect solo players from parties
- **Default**: `true`

#### `separateQueue` (boolean)
- **Description**: Separate queue for solo players
- **Default**: `false`

#### `maxPartySize` (number)
- **Description**: Max party size to match against solos
- **Default**: `2` (duos can match solos)

### `regionLocking` (object)

#### `enabled` (boolean)
- **Default**: `true`

#### `preferSameRegion` (boolean)
- **Description**: Prioritize same-region matches
- **Default**: `true`

#### `crossRegionThreshold` (number)
- **Type**: Integer (seconds)
- **Description**: Wait time before cross-region matching
- **Default**: `60` seconds

---

## Backfill System Object

### `enabled` (boolean)
- **Description**: Allow player replacement mid-match
- **Default**: `true`

### `allowBackfill` (boolean)
- **Description**: Players can join in-progress matches
- **Default**: `true`

### `backfillRewards` (object)
Bonus rewards for joining in-progress games.

#### `bonusGold` (number)
- **Type**: Integer
- **Description**: Extra gold for backfilling
- **Example**: `1000`

#### `bonusExp` (number)
- **Type**: Integer
- **Description**: Extra experience for backfilling
- **Example**: `100`

---

## Matchmaking Quality Score Example

### Ranked Match Quality Calculation
```
Player A: 2000 ELO, 5000 Power, 2 win streak
Player B: 2050 ELO, 5200 Power, 0 win streak

ELO Score: 0.7 * (1 - |2000-2050|/maxRange)
         = 0.7 * (1 - 50/500) = 0.7 * 0.9 = 0.63

Power Score: 0.2 * (1 - |5000-5200|/maxDifference)
          = 0.2 * (1 - 200/2000) = 0.2 * 0.9 = 0.18

Streak Score: 0.1 * (1 - |20-0|/100)
           = 0.1 * 0.8 = 0.08

Total Quality: 0.63 + 0.18 + 0.08 = 0.89 (89% match quality)
```

---

## Queue Time Expansion Example

### Search Timeline
```
Time 0s: Initial range ±100 ELO (1900-2100)
Time 10s: Expand to ±150 ELO (1850-2150)
Time 20s: Expand to ±200 ELO (1800-2200)
Time 30s: Expand to ±250 ELO (1750-2250)
...
Time 80s: Max range ±500 ELO (1500-2500)
Time 90s: Offer bot match
Time 120s: Force match or timeout
```

---

## Smurf Detection Example

### New Account Pattern
```
Games Played: 20
Wins: 18
Win Rate: 90% (above 80% threshold)
Action: AccelerateEloGain
Result: +50 ELO per win instead of +25
Effect: Rapidly climb to true skill level
```

---

## Data Validation Rules

1. **Weight Totals**: Sum of consideration weights should be ≤ 1.0
2. **Range Logic**: initial ≤ maxRange
3. **Timeout Ordering**: requeueDelay < botMatchThreshold < maxSearchTime
4. **Positive Values**: All ranges, timeouts, bonuses must be > 0
5. **Threshold Ranges**: Win rate thresholds must be 0.0-1.0
6. **Party Size**: min ≤ max for partySize
7. **Expansion Validity**: expansionRate > 0, expansionInterval > 0

---

## Related Configuration Files

- **ArenaConfig.json**: Uses matchmaking for PvP modes
- **BattleRules.json**: Battle settings post-match
- **SystemSettings.json**: PvP and timeout settings
- **FeatureFlags.json**: Can disable/enable matchmaking features
- **AIProfiles.json**: Bot AI difficulty scaling

---

## Version History
- **1.0.0** (2025-11-23): Initial schema documentation
