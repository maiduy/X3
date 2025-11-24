# SegmentConfig.json Schema Documentation

## Overview
Defines player segmentation rules, behavior targeting, personalized experiences, and A/B testing frameworks. Enables data-driven player classification and customized game experiences based on player behavior, spending patterns, engagement levels, and progression.

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

### `segments` (array)
- **Type**: Array of Segment Objects
- **Description**: Player segment definitions with criteria and overrides
- **Required**: Yes
- **Usage**: Core segmentation logic

### `segmentationRules` (object)
- **Description**: Global segmentation system rules
- **Required**: Yes
- **Usage**: System-wide behavior configuration

### `abTestGroups` (object)
- **Description**: A/B testing framework configuration
- **Required**: Yes
- **Usage**: Experiment management

### `fallbackBehavior` (object)
- **Description**: Error handling and fallback strategies
- **Required**: Yes
- **Usage**: Graceful degradation

---

## Segment Object

### `id` (string)
- **Format**: `SEGMENT_[NAME]`
- **Example**: `"SEGMENT_NEW_PLAYERS"`, `"SEGMENT_WHALES"`
- **Description**: Unique segment identifier
- **Required**: Yes

### `meta` (object)
Segment metadata and categorization.

#### `name` (string)
- **Type**: Display Text
- **Description**: Human-readable segment name
- **Example**: `"New Players"`, `"Dolphins (Light Spenders)"`
- **Usage**: UI display and analytics

#### `description` (string)
- **Type**: Display Text
- **Description**: Segment purpose and criteria summary
- **Example**: `"Players within first 7 days of gameplay"`
- **Usage**: Documentation and reporting

#### `category` (string)
- **Values**: `"Engagement"`, `"Monetization"`, `"Retention"`, `"Development"`, `"Behavior"`
- **Description**: Segment category for organization
- **Usage**: Analytics grouping

#### `priority` (number)
- **Type**: Integer (1-100)
- **Description**: Segment priority for conflict resolution
- **Default**: `50`
- **Usage**: Higher priority segments override lower priority

**Priority Tiers**:
- **1-20**: Critical segments (New Players, Beta Testers)
- **21-40**: Important segments (Spenders, Competitive)
- **41-60**: Standard segments (Churned, Casual)
- **61-100**: Low priority segments

### `criteria` (object)
Player matching criteria.

#### `accountAge` (object)
Account age in days.

##### `min` (number)
- **Type**: Integer (days)
- **Description**: Minimum account age
- **Example**: `7` = at least 7 days old

##### `max` (number)
- **Type**: Integer (days)
- **Description**: Maximum account age
- **Example**: `7` = at most 7 days old

**Example**: `{"min": 7, "max": 30}` = 7-30 days old

#### `playerLevel` (object)
Player level range.

##### `min` (number)
- **Type**: Integer
- **Description**: Minimum player level
- **Example**: `20`

##### `max` (number)
- **Type**: Integer
- **Description**: Maximum player level
- **Example**: `50`

#### `totalSpent` (object)
Total money spent (USD).

##### `min` (number)
- **Type**: Float (USD)
- **Description**: Minimum spending
- **Example**: `5.0` = spent at least $5

##### `max` (number)
- **Type**: Float (USD)
- **Description**: Maximum spending
- **Example**: `50.0` = spent at most $50

**Example**: `{"min": 5, "max": 50}` = $5-$50 spenders (Dolphins)

#### `dailyPlaytime` (object)
Daily playtime in minutes.

##### `min` (number)
- **Type**: Integer (minutes)
- **Description**: Minimum daily playtime
- **Example**: `60` = at least 1 hour/day

##### `max` (number)
- **Type**: Integer (minutes)
- **Description**: Maximum daily playtime
- **Example**: `180` = at most 3 hours/day

#### `lastLogin` (object)
Last login recency.

##### `within` (number)
- **Type**: Integer (days)
- **Description**: Logged in within X days
- **Example**: `7` = logged in within last 7 days

##### `min` (number)
- **Type**: Integer (days)
- **Description**: Last login at least X days ago
- **Example**: `7` = hasn't logged in for 7+ days

##### `max` (number)
- **Type**: Integer (days)
- **Description**: Last login at most X days ago
- **Example**: `30` = logged in within last 30 days

**Example**: `{"min": 7, "max": 30}` = last login 7-30 days ago (Churned)

#### `lastPurchase` (object)
Last purchase recency.

##### `within` (number)
- **Type**: Integer (days)
- **Description**: Made purchase within X days
- **Example**: `30` = purchased in last 30 days

#### `arenaMatches` (object)
PvP match participation.

##### `min` (number)
- **Type**: Integer
- **Description**: Minimum matches played
- **Example**: `20`

##### `period` (string)
- **Values**: `"daily"`, `"weekly"`, `"monthly"`, `"all_time"`
- **Description**: Time period for counting
- **Example**: `"weekly"` = 20 matches per week

**Example**: `{"min": 20, "period": "weekly"}` = 20+ weekly arena matches

#### `arenaRank` (object)
PvP rank requirement.

##### `min` (string)
- **Values**: `"Bronze"`, `"Silver"`, `"Gold"`, `"Platinum"`, `"Diamond"`, `"Master"`, `"Grandmaster"`
- **Description**: Minimum rank required
- **Example**: `"Gold"` = Gold rank or higher

#### `pvpWinRate` (object)
PvP win rate.

##### `min` (number)
- **Type**: Float (0.0-1.0)
- **Description**: Minimum win rate
- **Example**: `0.5` = 50%+ win rate

##### `max` (number)
- **Type**: Float (0.0-1.0)
- **Description**: Maximum win rate
- **Example**: `0.7` = up to 70% win rate

#### `betaTester` (boolean)
- **Description**: Beta tester flag
- **Example**: `true` = is beta tester
- **Usage**: Special access segments

### `overrides` (object)
Customizations applied to segment members.

#### `difficulty` (object)
Difficulty adjustments.

##### `stageMultiplier` (number)
- **Type**: Float
- **Description**: Stage difficulty multiplier
- **Example**: `0.8` = 20% easier, `1.2` = 20% harder
- **Range**: `0.5-1.5` (reasonable range)

##### `enemyDamageReduction` (number)
- **Type**: Float (0.0-1.0)
- **Description**: Enemy damage reduction
- **Example**: `0.2` = enemies deal 20% less damage

##### `rewardMultiplier` (number)
- **Type**: Float
- **Description**: Reward multiplier
- **Example**: `1.1` = 10% more rewards

#### `rewards` (object)
Reward modifications.

##### `goldMultiplier` (number)
- **Type**: Float
- **Description**: Gold drop multiplier
- **Example**: `1.5` = 50% more gold

##### `expMultiplier` (number)
- **Type**: Float
- **Description**: Experience gain multiplier
- **Example**: `1.5` = 50% more EXP

##### `dailyLoginBonus` (number)
- **Type**: Float
- **Description**: Daily login bonus multiplier
- **Example**: `1.2` = 20% more daily rewards

##### `achievementRewards` (number)
- **Type**: Float
- **Description**: Achievement reward multiplier
- **Example**: `1.1` = 10% more achievement rewards

##### `comebackBonus` (object)
Special comeback rewards.

###### `enabled` (boolean)
- **Description**: Enable comeback bonus
- **Default**: `false`

###### `rewards` (array)
- **Type**: Array of Reward Objects
- **Structure**: `[{"itemId": "...", "amount": number}]`
- **Example**:
```json
[
  {"itemId": "CURRENCY_GEM", "amount": 500},
  {"itemId": "ITEM_SUMMON_SCROLL", "amount": 10}
]
```

##### `testingCompensation` (object)
- **Description**: Beta tester rewards
- **Structure**: `{"itemId": amount}`
- **Example**: `{"CURRENCY_GEM": 1000}`

#### `shop` (object)
Shop customizations.

##### `starterPacksVisible` (boolean)
- **Description**: Show starter packs
- **Default**: `false`
- **Usage**: New player offers

##### `lowPriceOffersVisible` (boolean)
- **Description**: Show low-price offers
- **Default**: `false`
- **Usage**: F2P conversion offers

##### `specialOffers` (array)
- **Type**: Array of Offer IDs
- **Description**: Special offers to display
- **Example**: `["OFFER_BEGINNER_BUNDLE", "OFFER_MONTHLY_PACK"]`

##### `displayPriority` (array)
- **Type**: Array of Priority Tags
- **Description**: Offer display priority order
- **Values**: `["BestValue", "LimitedTime", "Premium", "Exclusive"]`
- **Example**: `["BestValue", "LimitedTime"]`

##### `vipBenefits` (boolean)
- **Description**: Enable VIP benefits
- **Default**: `false`
- **Usage**: Spender perks

##### `premiumCurrencyBonus` (number)
- **Type**: Float
- **Description**: Premium currency purchase bonus
- **Example**: `1.2` = 20% more gems on purchase

##### `arenaCurrencyBonus` (number)
- **Type**: Float
- **Description**: Arena currency bonus
- **Example**: `1.2` = 20% more arena coins

#### `gacha` (object)
Gacha system modifications.

##### `softPityAdjustment` (number)
- **Type**: Integer
- **Description**: Soft pity threshold adjustment
- **Example**: `-5` = pity 5 pulls earlier
- **Usage**: Improve odds for target segments

##### `firstSSRGuarantee` (number)
- **Type**: Integer (pulls)
- **Description**: Guaranteed SSR within X pulls
- **Example**: `50` = guaranteed SSR in 50 pulls
- **Usage**: New player retention

#### `events` (object)
Event access modifications.

##### `exclusiveAccess` (array)
- **Type**: Array of Event IDs
- **Description**: Exclusive events for this segment
- **Example**: `["EVENT_VIP_DUNGEON", "EVENT_WHALE_RAID"]`

##### `priorityMatchmaking` (boolean)
- **Description**: Priority in event matchmaking
- **Default**: `false`
- **Usage**: Whale benefits

#### `matchmaking` (object)
Matchmaking customizations.

##### `priorityQueue` (boolean)
- **Description**: Priority in matchmaking queue
- **Default**: `false`
- **Usage**: Reduce wait times for VIPs

##### `reducedWaitTime` (boolean)
- **Description**: Reduce matchmaking wait time
- **Default**: `false`

#### `support` (object)
Customer support customizations.

##### `priorityQueue` (boolean)
- **Description**: Priority in support queue
- **Default**: `false`
- **Usage**: Whale/VIP support

##### `dedicatedSupport` (boolean)
- **Description**: Dedicated support agent
- **Default**: `false`
- **Usage**: High-value player retention

##### `directFeedbackChannel` (boolean)
- **Description**: Direct feedback channel access
- **Default**: `false`
- **Usage**: Beta testers

##### `bugReportRewards` (boolean)
- **Description**: Rewards for bug reports
- **Default**: `false`
- **Usage**: Beta tester incentives

#### `features` (object)
Feature access modifications.

##### `earlyAccess` (array)
- **Type**: Array of Feature IDs
- **Description**: Early access to features
- **Example**: `["FEATURE_GUILD_SYSTEM"]`
- **Usage**: Beta testers, VIPs

##### `debugTools` (boolean)
- **Description**: Enable debug tools
- **Default**: `false`
- **Usage**: Beta testers only

#### `notifications` (object)
Notification customizations.

##### `specialMessages` (array)
- **Type**: Array of Message Keys
- **Description**: Special message keys
- **Example**: `["MSG_COMEBACK_WE_MISS_YOU"]`

##### `pushNotifications` (boolean)
- **Description**: Enable push notifications
- **Default**: `false`
- **Usage**: Re-engagement campaigns

### `active` (boolean)
- **Description**: Whether segment is active
- **Default**: `true`
- **Usage**: Disable segments without deleting

---

## Segmentation Rules Object

### `evaluationFrequency` (number)
- **Type**: Integer (seconds)
- **Description**: How often to re-evaluate player segments
- **Default**: `3600` (1 hour)
- **Usage**: Balance accuracy vs performance
- **Example**: `3600` = hourly, `86400` = daily

### `priorityStrategy` (string)
- **Values**: `"HighestPriorityWins"`, `"LowestPriorityWins"`, `"MergeOverrides"`
- **Description**: Conflict resolution strategy
- **Default**: `"HighestPriorityWins"`

**Strategy Descriptions**:
- **HighestPriorityWins**: Highest priority segment overrides all others
- **LowestPriorityWins**: Lowest priority segment takes precedence (rare)
- **MergeOverrides**: Merge all applicable segment overrides

### `allowMultipleSegments` (boolean)
- **Description**: Allow players in multiple segments
- **Default**: `true`
- **True**: Players can match multiple segments
- **False**: Players assigned to single segment only

### `maxSegmentsPerUser` (number)
- **Type**: Integer
- **Description**: Maximum segments per player
- **Default**: `3`
- **Usage**: Performance optimization
- **Example**: `3` = player can be in up to 3 segments

### `cacheDuration` (number)
- **Type**: Integer (seconds)
- **Description**: Cache segment evaluation results
- **Default**: `1800` (30 minutes)
- **Usage**: Reduce computation frequency

---

## A/B Test Groups Object

### `enabled` (boolean)
- **Description**: Enable A/B testing system
- **Default**: `true`
- **Usage**: Master toggle for experiments

### `tests` (array)
Array of active A/B tests.

#### A/B Test Object

##### `id` (string)
- **Format**: `AB_TEST_[NAME]`
- **Example**: `"AB_TEST_GACHA_RATES_V2"`
- **Required**: Yes

##### `name` (string)
- **Type**: Display Text
- **Description**: Test name
- **Example**: `"Gacha Rate Test V2"`

##### `description` (string)
- **Type**: Display Text
- **Description**: Test purpose
- **Example**: `"Test 2% vs 3% SSR rates for new players"`

##### `status` (string)
- **Values**: `"Active"`, `"Paused"`, `"Completed"`, `"Draft"`
- **Description**: Test lifecycle status
- **Default**: `"Draft"`

##### `startDate` (string)
- **Type**: ISO 8601 DateTime
- **Description**: Test start time
- **Example**: `"2025-12-01T00:00:00Z"`

##### `endDate` (string)
- **Type**: ISO 8601 DateTime
- **Description**: Test end time
- **Example**: `"2025-12-15T23:59:59Z"`

##### `targetSegments` (array)
- **Type**: Array of Segment IDs
- **Description**: Which segments this test applies to
- **Example**: `["SEGMENT_NEW_PLAYERS"]`
- **Usage**: Scope test to specific player groups

##### `variants` (array)
Test variant configurations.

###### Variant Object

**`id`** (string)
- **Values**: `"Control"`, `"TestA"`, `"TestB"`, etc.
- **Description**: Variant identifier
- **Required**: Yes

**`percentage`** (number)
- **Type**: Integer (0-100)
- **Description**: Player distribution percentage
- **Example**: `50` = 50% of players
- **Total**: All variants must sum to 100

**`overrides`** (object)
- **Description**: Variant-specific overrides
- **Structure**: Same as segment overrides
- **Example**:
```json
{
  "gachaRates": {
    "SSR": 0.03
  }
}
```

##### `metrics` (array)
- **Type**: Array of Metric IDs
- **Description**: Success metrics to track
- **Examples**: `["METRIC_RETENTION_D7", "METRIC_CONVERSION_RATE", "METRIC_ARPU"]`

---

## Fallback Behavior Object

### `onSegmentationFailure` (string)
- **Values**: `"UseDefault"`, `"RetryOnce"`, `"SkipSegmentation"`
- **Description**: Action on segmentation error
- **Default**: `"UseDefault"`

**Actions**:
- **UseDefault**: Apply default configuration (no overrides)
- **RetryOnce**: Retry segmentation once, then use default
- **SkipSegmentation**: Continue without segmentation

### `onOverrideError` (string)
- **Values**: `"IgnoreOverride"`, `"UseDefault"`, `"FailSafe"`
- **Description**: Action when override fails
- **Default**: `"IgnoreOverride"`

**Actions**:
- **IgnoreOverride**: Skip failed override, continue with others
- **UseDefault**: Revert to default configuration
- **FailSafe**: Apply minimal safe overrides

### `enableLogging` (boolean)
- **Description**: Log segmentation decisions
- **Default**: `true`
- **Usage**: Debugging and analytics

### `enableMetrics` (boolean)
- **Description**: Track segmentation metrics
- **Default**: `true`
- **Usage**: Performance monitoring

---

## Player Segment Examples

### New Players Segment
```json
{
  "id": "SEGMENT_NEW_PLAYERS",
  "criteria": {
    "accountAge": {"max": 7},
    "playerLevel": {"max": 20}
  },
  "overrides": {
    "difficulty": {
      "stageMultiplier": 0.8,
      "enemyDamageReduction": 0.2
    },
    "rewards": {
      "goldMultiplier": 1.5,
      "expMultiplier": 1.5
    }
  }
}
```

**Purpose**: Onboarding retention
**Effect**: 20% easier difficulty, 50% more rewards
**Duration**: First 7 days

### F2P Grinders Segment
```json
{
  "id": "SEGMENT_F2P_GRINDERS",
  "criteria": {
    "totalSpent": {"max": 0},
    "accountAge": {"min": 7},
    "dailyPlaytime": {"min": 60}
  },
  "overrides": {
    "shop": {
      "specialOffers": ["OFFER_FIRST_TIME_BUYER_0.99"]
    },
    "gacha": {
      "softPityAdjustment": -5,
      "firstSSRGuarantee": 50
    }
  }
}
```

**Purpose**: Convert F2P to payers
**Effect**: Low-price offers, better gacha odds
**Target**: Active non-payers

### Whale Segment
```json
{
  "id": "SEGMENT_WHALES",
  "criteria": {
    "totalSpent": {"min": 50},
    "lastPurchase": {"within": 30}
  },
  "overrides": {
    "shop": {
      "premiumCurrencyBonus": 1.2,
      "specialOffers": ["OFFER_WHALE_PACK_99.99"]
    },
    "events": {
      "exclusiveAccess": ["EVENT_WHALE_RAID"],
      "priorityMatchmaking": true
    },
    "support": {
      "priorityQueue": true,
      "dedicatedSupport": true
    }
  }
}
```

**Purpose**: Whale retention
**Effect**: Exclusive content, VIP treatment
**Value**: High-value player perks

### Churned Players Segment
```json
{
  "id": "SEGMENT_CHURNED_PLAYERS",
  "criteria": {
    "lastLogin": {"min": 7, "max": 30}
  },
  "overrides": {
    "rewards": {
      "comebackBonus": {
        "enabled": true,
        "rewards": [
          {"itemId": "CURRENCY_GEM", "amount": 500},
          {"itemId": "ITEM_SUMMON_SCROLL", "amount": 10}
        ]
      }
    },
    "notifications": {
      "pushNotifications": true,
      "specialMessages": ["MSG_COMEBACK_WE_MISS_YOU"]
    }
  }
}
```

**Purpose**: Win-back campaign
**Effect**: Comeback rewards, re-engagement notifications
**Target**: Recently inactive players

---

## Segmentation Evaluation Flow

### Step 1: Player Login
```
Player ID: user_12345
Action: Login to game
```

### Step 2: Fetch Cached Segments
```
Check cache: Last evaluation 20 minutes ago
cacheDuration: 1800 seconds (30 minutes)
Result: Use cached segments
```

### Step 3: Cached Segments Expired
```
Last evaluation: 35 minutes ago
cacheDuration: 30 minutes
Result: Re-evaluate segments
```

### Step 4: Evaluate Criteria
```
Player Data:
- accountAge: 5 days
- playerLevel: 15
- totalSpent: $0
- dailyPlaytime: 90 minutes

Segment 1: SEGMENT_NEW_PLAYERS
- accountAge: max 7 ✓
- playerLevel: max 20 ✓
Result: MATCH

Segment 2: SEGMENT_F2P_GRINDERS
- totalSpent: max 0 ✓
- accountAge: min 7 ✗ (only 5 days)
Result: NO MATCH

Final Segments: [SEGMENT_NEW_PLAYERS]
```

### Step 5: Apply Overrides
```
Active Segments: [SEGMENT_NEW_PLAYERS]
Priority: 10

Overrides Applied:
- difficulty.stageMultiplier = 0.8
- difficulty.enemyDamageReduction = 0.2
- rewards.goldMultiplier = 1.5
- rewards.expMultiplier = 1.5
- shop.starterPacksVisible = true
```

### Step 6: Cache Results
```
Cache segment evaluation for 30 minutes
Next evaluation: 30 minutes from now or manual trigger
```

---

## Multi-Segment Conflict Resolution

### Scenario: Player Matches Multiple Segments
```
Player Data:
- accountAge: 30 days
- totalSpent: $10
- lastPurchase: 5 days ago
- arenaMatches: 25/week

Matching Segments:
1. SEGMENT_DOLPHINS (priority 30)
   - totalSpent: $5-$50 ✓
   - lastPurchase: within 30 days ✓

2. SEGMENT_COMPETITIVE_PLAYERS (priority 25)
   - arenaMatches: 20+/week ✓

allowMultipleSegments: true
maxSegmentsPerUser: 3
priorityStrategy: HighestPriorityWins
```

### Resolution Strategy: HighestPriorityWins
```
Both segments apply:
1. SEGMENT_COMPETITIVE_PLAYERS (priority 25) - HIGHER
2. SEGMENT_DOLPHINS (priority 30)

Conflict on shop.specialOffers:
- DOLPHINS: ["OFFER_MONTHLY_PACK"]
- COMPETITIVE: ["OFFER_PVP_BUNDLE"]

Result: Use COMPETITIVE (higher priority)
Final: ["OFFER_PVP_BUNDLE"]
```

### Resolution Strategy: MergeOverrides
```
Merge all overrides from both segments:

shop.specialOffers:
- DOLPHINS: ["OFFER_MONTHLY_PACK"]
- COMPETITIVE: ["OFFER_PVP_BUNDLE"]
Result: ["OFFER_PVP_BUNDLE", "OFFER_MONTHLY_PACK"]

difficulty.stageMultiplier:
- DOLPHINS: 0.95
- COMPETITIVE: none
Result: 0.95
```

---

## A/B Testing Example

### Test Setup
```json
{
  "id": "AB_TEST_GACHA_RATES_V2",
  "status": "Active",
  "targetSegments": ["SEGMENT_NEW_PLAYERS"],
  "variants": [
    {
      "id": "Control",
      "percentage": 50,
      "overrides": {
        "gachaRates": {"SSR": 0.02}
      }
    },
    {
      "id": "TestA",
      "percentage": 50,
      "overrides": {
        "gachaRates": {"SSR": 0.03}
      }
    }
  ],
  "metrics": ["METRIC_RETENTION_D7", "METRIC_CONVERSION_RATE"]
}
```

### Player Assignment
```
New Player joins:
- Player ID: user_12345
- Segment: SEGMENT_NEW_PLAYERS

Check A/B Tests targeting SEGMENT_NEW_PLAYERS:
- Test found: AB_TEST_GACHA_RATES_V2

Assign variant:
- Hash player ID: hash("user_12345") % 100 = 73
- Control: 0-49 (50%)
- TestA: 50-99 (50%)
- Result: Player in TestA (73 is in 50-99 range)

Apply overrides:
- Base SSR rate: 0.02 (2%)
- Segment override: none
- A/B Test override: 0.03 (3%)
- Final SSR rate: 3%
```

### Metrics Tracking
```
Variant: Control (2% SSR)
- Players: 500
- D7 Retention: 45%
- Conversion Rate: 8%

Variant: TestA (3% SSR)
- Players: 500
- D7 Retention: 52%
- Conversion Rate: 12%

Analysis:
- TestA shows +7% retention
- TestA shows +4% conversion
- Winner: TestA (3% SSR rate)
- Decision: Deploy 3% rate to all new players
```

---

## Common Use Cases

### New Player Onboarding
```
Goal: Improve new player retention
Segment: SEGMENT_NEW_PLAYERS
Duration: First 7 days
Overrides:
- 20% easier difficulty
- 50% more gold and EXP
- Starter packs visible
- First SSR guaranteed in 50 pulls

Expected Impact:
- +15% D7 retention
- +10% D14 retention
```

### F2P Conversion
```
Goal: Convert free players to payers
Segment: SEGMENT_F2P_GRINDERS
Criteria: Active F2P players
Overrides:
- Low-price offers ($0.99)
- Improved gacha odds
- Daily bonus increased 20%

Expected Impact:
- +5% conversion rate
- $0.50 average first purchase value
```

### Whale Retention
```
Goal: Retain high-value players
Segment: SEGMENT_WHALES
Criteria: $50+/month spenders
Overrides:
- Exclusive content access
- Priority support
- 20% premium currency bonus
- VIP matchmaking

Expected Impact:
- +20% retention
- +15% lifetime value
```

### Win-Back Campaign
```
Goal: Re-engage churned players
Segment: SEGMENT_CHURNED_PLAYERS
Criteria: No login 7-30 days
Overrides:
- 500 gems comeback bonus
- 10 summon scrolls
- Push notifications
- Special events

Expected Impact:
- 30% return rate
- 50% of returned players stay active
```

---

## Data Validation Rules

1. **Segment IDs**:
   - Must be unique across all segments
   - Format: SEGMENT_[NAME]

2. **Priority Values**:
   - Must be 1-100
   - Unique priorities recommended (not required)

3. **Criteria Ranges**:
   - min ≤ max for all range criteria
   - All numeric values ≥ 0

4. **Override Multipliers**:
   - stageMultiplier: 0.5-1.5 (reasonable range)
   - Reward multipliers: 1.0-3.0 (reasonable range)
   - Damage reduction: 0.0-0.5 (max 50%)

5. **A/B Test Variants**:
   - Percentages must sum to 100
   - At least 2 variants required
   - startDate < endDate

6. **Segmentation Rules**:
   - evaluationFrequency: 60-86400 seconds
   - cacheDuration < evaluationFrequency
   - maxSegmentsPerUser: 1-10

7. **Active Flag**:
   - All segments should have active field
   - Inactive segments ignored in evaluation

---

## Design Guidelines

### Segment Definition
- **Clear Criteria**: Unambiguous matching rules
- **Non-Overlapping**: Minimize segment overlap when possible
- **Priority Assignment**: Higher priority for more specific segments
- **Testing**: Test criteria logic before deployment

### Override Values
- **Conservative**: Start with small adjustments (10-20%)
- **A/B Test**: Test significant changes before full rollout
- **Monitor**: Track metrics for each segment
- **Balance**: Avoid making game too easy/hard

### Monetization Segments
- **Tiered Approach**: Bronze/Silver/Gold/Platinum tiers
- **Recency**: Recent purchase = more valuable
- **Graduation**: Move players up tiers as they spend more
- **Retention**: Exclusive benefits for high spenders

### Engagement Segments
- **Behavioral**: Based on actions, not just time
- **Rewarding**: Positive reinforcement for engagement
- **Progressive**: Increase benefits with continued engagement
- **Re-engagement**: Win-back strategies for lapsed players

### A/B Testing
- **Hypothesis**: Clear hypothesis before testing
- **Sample Size**: Sufficient players for statistical significance
- **Duration**: Run tests for 7-14 days minimum
- **Metrics**: Track multiple metrics (retention, monetization, engagement)
- **Winner**: Deploy winning variant to all players

---

## Performance Considerations

### Evaluation Frequency
```
High frequency (5 minutes):
- Pros: Real-time segmentation
- Cons: High CPU usage, database load
- Use case: Critical segments (maintenance, kill switches)

Medium frequency (1 hour):
- Pros: Balance accuracy and performance
- Cons: Slight delay in segment changes
- Use case: Most segments (recommended)

Low frequency (24 hours):
- Pros: Minimal performance impact
- Cons: Slow to react to changes
- Use case: Stable segments (account age, spending tier)
```

### Cache Strategy
```
Aggressive caching (30 minutes):
- Reduces database queries
- Faster segment lookups
- Acceptable staleness for most use cases

Minimal caching (5 minutes):
- Near real-time accuracy
- Higher database load
- Use for time-sensitive segments
```

### Query Optimization
```
Indexed fields:
- playerLevel
- totalSpent
- lastLogin
- accountAge

Avoid complex queries:
- Multiple JOINs
- Subqueries
- Full table scans

Use materialized views:
- Pre-compute segment membership
- Refresh periodically
```

---

## Security and Privacy

### Data Access
- Limit segment criteria to non-PII data
- Aggregate analytics only
- No individual player profiling in UI

### A/B Testing Ethics
- Transparent about testing
- No harmful experiments (excessive difficulty, reduced rewards)
- Ability to opt-out (privacy settings)

### Monetization
- Fair pricing across segments
- No predatory practices
- Clear value propositions

---

## Related Configuration Files

- **FeatureFlags.json**: Feature gates referenced by segment overrides
- **GlobalModifiers.json**: Event modifiers can target segments
- **SystemSettings.json**: Base values modified by segment overrides
- **ArenaConfig.json**: PvP settings affected by competitive segments
- **Rewards.json**: Reward tables modified by segment multipliers
- **Characters.json**: Character access controlled by segment features
- **EventSchedule.json**: Event access controlled by segment criteria

---

## Version History
- **1.0.0** (2025-11-23): Initial schema documentation
