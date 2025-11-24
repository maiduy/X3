# Economy.json Schema Documentation

## Overview
Defines the game's economic system including currencies, resources, balance targets, exchange rates, and inflation control. Provides the foundation for all in-game economic activity and progression pacing.

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

### `currencies` (array)
- **Type**: Array of Currency Objects
- **Description**: All currency definitions
- **Required**: Yes

### `resources` (array)
- **Type**: Array of Resource Objects
- **Description**: Regenerating resources (energy, stamina, etc.)
- **Required**: Yes

### `balanceTargets` (object)
- **Description**: Target economy balance by player progression
- **Required**: Yes

### `exchangeRates` (object)
- **Description**: Currency/resource conversion rates
- **Required**: Yes

### `inflationControl` (object)
- **Description**: Inflation monitoring and control settings
- **Required**: Yes

---

## Currency Object

### `id` (string)
- **Format**: `CURRENCY_[NAME]`
- **Example**: `"CURRENCY_GOLD"`, `"CURRENCY_GEM"`, `"CURRENCY_ARENA_COIN"`
- **Description**: Unique currency identifier
- **Required**: Yes

### `meta` (object)
Currency metadata.

#### `nameKey` (string)
- **Type**: Localization Key
- **Description**: Currency name localization key
- **Format**: `CURRENCY_NAME_[CURRENCY_ID]`
- **Example**: `"CURRENCY_NAME_GOLD"`

#### `icon` (string)
- **Type**: Asset Reference
- **Description**: Currency icon asset path
- **Example**: `"Icon_Currency_Gold"`

#### `type` (string)
- **Values**: `"Soft"`, `"Premium"`, `"Event"`, `"Social"`
- **Description**: Currency category type
- **Required**: Yes

**Currency Types**:
- **Soft**: Earned through gameplay (Gold, EXP)
- **Premium**: Primarily obtained via IAP (Gems, Crystals)
- **Event**: Specific to events/modes (Arena Coins, Guild Coins)
- **Social**: Social features (Friend Points)

#### `rarity` (string)
- **Values**: `"Common"`, `"Rare"`, `"Premium"`, `"Epic"`, `"Legendary"`
- **Description**: Currency rarity/value tier
- **Default**: `"Common"`

### `limits` (object)
Currency storage and transfer limits.

#### `maxAmount` (number)
- **Type**: Integer
- **Description**: Maximum amount player can hold
- **Example**: `999999999` (soft currencies), `999999` (event currencies)
- **Required**: Yes
- **Usage**: Prevent integer overflow and hoarding

#### `transferable` (boolean)
- **Description**: Whether currency can be traded/gifted
- **Default**: `false`
- **True**: Allow player-to-player transfer
- **False**: Account-bound
- **Recommendation**: `false` for all currencies (prevents real money trading)

### `sources` (array)
Currency income sources.

#### Source Object

##### `type` (string)
- **Values**: `"StageCompletion"`, `"DailyQuest"`, `"Selling"`, `"IAP"`, `"Quest"`, `"Event"`, `"ArenaVictory"`, `"WeeklyRewards"`
- **Description**: How currency is earned
- **Required**: Yes

##### `avgPerRun` (number)
- **Type**: Integer
- **Description**: Average earned per instance
- **Example**: `150` gold per stage
- **Optional**: For per-instance sources

##### `avgPerDay` (number)
- **Type**: Integer
- **Description**: Average daily income
- **Example**: `15000` gold per day from dailies
- **Optional**: For daily sources

##### `avgPerWeek` (number)
- **Type**: Integer
- **Description**: Average weekly income
- **Example**: `500` gems per week from events
- **Optional**: For weekly sources

##### `avgPerMatch` (number)
- **Type**: Integer
- **Description**: Average per match/battle
- **Example**: `15` arena coins per victory
- **Optional**: For per-match sources

##### `primary` (boolean)
- **Description**: Whether this is the primary source
- **Default**: `false`
- **Usage**: IAP is primary for premium currency

##### `variable` (boolean)
- **Description**: Whether amount varies significantly
- **Default**: `false`
- **Example**: Selling items = variable income

### `sinks` (array)
Currency spending/consumption.

#### Sink Object

##### `type` (string)
- **Values**: `"Upgrade"`, `"Shop"`, `"Gacha"`, `"Refresh"`, `"ArenaShop"`
- **Description**: Where currency is spent
- **Required**: Yes

##### `avgPerDay` (number)
- **Type**: Integer
- **Description**: Average daily spending
- **Example**: `50000` gold per day on upgrades
- **Optional**: For estimating burn rate

##### `primary` (boolean)
- **Description**: Whether this is the main sink
- **Default**: `false`

### `dailyEconomy` (object)
Daily income/spending balance targets.

#### `targetIncome` (number)
- **Type**: Integer
- **Description**: Target daily income
- **Example**: `100000` gold per day
- **Usage**: Balance tuning

#### `targetSpending` (number)
- **Type**: Integer
- **Description**: Target daily spending
- **Example**: `80000` gold per day
- **Usage**: Net positive economy

#### `inflationRate` (number)
- **Type**: Float
- **Description**: Monthly inflation rate
- **Example**: `0.02` = 2% per month
- **Usage**: Account for power creep

### `conversionRates` (object)
Conversion to other currencies/resources.

#### Keys
- Currency/Resource IDs: `"toGold"`, `"toEnergy"`, etc.
- Values: Conversion ratios

**Example**:
```json
{
  "toGold": 1000,
  "toEnergy": 10
}
```

**Meaning**: 1 gem = 1,000 gold OR 10 energy

### `iapExchangeRate` (number)
- **Type**: Float
- **Description**: Real money to currency exchange rate
- **Example**: `1.0` = 1 gem per $0.01 (100 gems per dollar)
- **Usage**: Monetization balance

---

## Resource Object

### `id` (string)
- **Format**: `RESOURCE_[NAME]`
- **Example**: `"RESOURCE_ENERGY"`, `"RESOURCE_STAMINA"`
- **Description**: Unique resource identifier
- **Required**: Yes

### `meta` (object)
Resource metadata.

#### `nameKey` (string)
- **Type**: Localization Key
- **Example**: `"RESOURCE_NAME_ENERGY"`

#### `icon` (string)
- **Type**: Asset Reference
- **Example**: `"Icon_Resource_Energy"`

#### `type` (string)
- **Values**: `"Regenerating"`, `"Consumable"`, `"Permanent"`
- **Description**: Resource type
- **Default**: `"Regenerating"`

### `limits` (object)
Resource storage limits.

#### `maxAmount` (number)
- **Type**: Integer
- **Description**: Maximum amount (with overflow)
- **Example**: `200` energy
- **Usage**: Cap with bonus items

#### `naturalCap` (number)
- **Type**: Integer
- **Description**: Natural regeneration cap
- **Example**: `100` energy
- **Usage**: Stop regen at this point (can exceed with items)

### `regeneration` (object)
Auto-regeneration configuration.

#### `rate` (number)
- **Type**: Integer
- **Description**: Amount regenerated per interval
- **Example**: `1` energy per interval
- **Default**: `1`

#### `interval` (number)
- **Type**: Integer (seconds)
- **Description**: Time between regenerations
- **Example**: `300` = 5 minutes
- **Standard**: 300-600 seconds (5-10 minutes)

#### `bonusFromLevel` (number)
- **Type**: Integer
- **Description**: Bonus regen per player level
- **Example**: `0` = no level bonus
- **Usage**: Scale regen with progression

**Regeneration Calculation**:
```
Energy per day = (86400 / interval) × rate
Example: (86400 / 300) × 1 = 288 energy per day
```

### `sources` (array)
Additional resource sources.

#### Source Object

##### `type` (string)
- **Values**: `"Regeneration"`, `"LevelUp"`, `"DailyReward"`
- **Description**: How resource is gained

##### `perDay` (number)
- **Type**: Integer
- **Description**: Daily amount from this source
- **Example**: `288` from regeneration

##### `amount` (number)
- **Type**: Integer
- **Description**: Fixed amount per trigger
- **Example**: `100` energy on level up

### `costs` (object)
Resource consumption costs.

#### Keys
- Activity types: `"normalStage"`, `"hardStage"`, `"dungeon"`, `"raid"`
- Values: Energy cost

**Example**:
```json
{
  "normalStage": 6,
  "hardStage": 8,
  "dungeon": 15
}
```

---

## Balance Targets Object

### Player Progression Tiers

#### `newPlayer` (object)
Early game balance (levels 1-20).

##### `level` (string)
- **Type**: Level Range
- **Description**: Level range definition
- **Example**: `"1-20"`

##### `dailyGold` (number)
- **Type**: Integer
- **Description**: Target daily gold income
- **Example**: `50000`

##### `dailyGems` (number)
- **Type**: Integer
- **Description**: Target daily gem income (F2P)
- **Example**: `30`

##### `progressionRate` (number)
- **Type**: Float
- **Description**: Relative progression speed
- **Example**: `1.5` = 50% faster than baseline
- **Usage**: New players progress faster

#### `midPlayer` (object)
Mid game balance (levels 21-50).

**Fields**: Same as newPlayer

**Example Values**:
```json
{
  "level": "21-50",
  "dailyGold": 150000,
  "dailyGems": 100,
  "progressionRate": 1.0
}
```

**Characteristics**: Baseline progression, stable economy

#### `endPlayer` (object)
End game balance (level 51+).

**Example Values**:
```json
{
  "level": "51+",
  "dailyGold": 500000,
  "dailyGems": 300,
  "progressionRate": 0.5
}
```

**Characteristics**: Slower progression, higher income, end game grind

---

## Exchange Rates Object

### `gemToGold` (number)
- **Type**: Integer
- **Description**: Gold per 1 gem
- **Example**: `1000` = 1 gem = 1,000 gold
- **Usage**: Shop conversions

### `gemToEnergy` (number)
- **Type**: Integer
- **Description**: Energy per 1 gem
- **Example**: `10` = 1 gem = 10 energy
- **Usage**: Energy refill pricing

### `goldToEnergy` (number | null)
- **Type**: Integer or null
- **Description**: Energy per X gold
- **Example**: `null` = cannot convert gold to energy
- **Null**: Conversion not available
- **Design**: Usually null (prevent gold → energy farming)

---

## Inflation Control Object

### `enabled` (boolean)
- **Description**: Enable inflation monitoring and control
- **Default**: `true`
- **Usage**: Track and manage economy inflation

### `monthlyInflationTarget` (number)
- **Type**: Float
- **Description**: Target monthly inflation rate
- **Example**: `0.05` = 5% per month
- **Usage**: Acceptable inflation range

### `adjustmentMethods` (array)
- **Type**: Array of Adjustment Method Names
- **Description**: How to control inflation
- **Values**: `["EventRewards", "ShopPrices", "DropRates"]`
- **Usage**: Levers for inflation control

**Adjustment Methods**:
- **EventRewards**: Increase/decrease event rewards
- **ShopPrices**: Adjust shop pricing
- **DropRates**: Modify drop rates

---

## Currency Examples

### Soft Currency (Gold)
```json
{
  "id": "CURRENCY_GOLD",
  "meta": {
    "type": "Soft",
    "rarity": "Common"
  },
  "limits": {
    "maxAmount": 999999999,
    "transferable": false
  },
  "sources": [
    {"type": "StageCompletion", "avgPerRun": 150},
    {"type": "DailyQuest", "avgPerDay": 15000}
  ],
  "sinks": [
    {"type": "Upgrade", "avgPerDay": 50000},
    {"type": "Shop", "avgPerDay": 20000}
  ],
  "dailyEconomy": {
    "targetIncome": 100000,
    "targetSpending": 80000
  }
}
```

**Daily Balance**:
- Income: 100,000 gold/day
- Spending: 80,000 gold/day
- **Net**: +20,000 gold/day (accumulation)

### Premium Currency (Gems)
```json
{
  "id": "CURRENCY_GEM",
  "meta": {
    "type": "Premium",
    "rarity": "Premium"
  },
  "limits": {
    "maxAmount": 999999999,
    "transferable": false
  },
  "sources": [
    {"type": "IAP", "primary": true},
    {"type": "Quest", "avgPerDay": 50},
    {"type": "Event", "avgPerWeek": 500}
  ],
  "sinks": [
    {"type": "Gacha", "avgPerDay": 320},
    {"type": "Shop", "avgPerDay": 100}
  ],
  "conversionRates": {
    "toGold": 1000,
    "toEnergy": 10
  },
  "iapExchangeRate": 1.0
}
```

**F2P Daily Income**:
- Quests: 50 gems/day
- Events: ~71 gems/day (500/week)
- **Total**: ~121 gems/day F2P

**Conversion Power**:
- 1 gem = 1,000 gold OR 10 energy
- 121 gems/day = 121,000 gold OR 1,210 energy

### Event Currency (Arena Coins)
```json
{
  "id": "CURRENCY_ARENA_COIN",
  "meta": {
    "type": "Event",
    "rarity": "Rare"
  },
  "limits": {
    "maxAmount": 999999,
    "transferable": false
  },
  "sources": [
    {"type": "ArenaVictory", "avgPerMatch": 15},
    {"type": "WeeklyRewards", "avgPerWeek": 200}
  ],
  "sinks": [
    {"type": "ArenaShop", "primary": true}
  ]
}
```

**Weekly Income**:
- Victories: 15 coins/match × 50 matches = 750 coins
- Rewards: 200 coins
- **Total**: 950 coins/week

---

## Resource Example (Energy)

```json
{
  "id": "RESOURCE_ENERGY",
  "meta": {
    "type": "Regenerating"
  },
  "limits": {
    "maxAmount": 200,
    "naturalCap": 100
  },
  "regeneration": {
    "rate": 1,
    "interval": 300
  },
  "sources": [
    {"type": "Regeneration", "perDay": 288},
    {"type": "LevelUp", "amount": 100},
    {"type": "DailyReward", "amount": 60}
  ],
  "costs": {
    "normalStage": 6,
    "hardStage": 8,
    "dungeon": 15
  }
}
```

**Daily Energy Budget**:
- Regeneration: 288 energy/day
- Daily Reward: 60 energy
- **Total**: 348 energy/day (F2P)

**Daily Activities**:
- Normal Stages: 348 / 6 = 58 stages/day
- Hard Stages: 348 / 8 = 43 stages/day
- Dungeons: 348 / 15 = 23 dungeons/day

**With Refills**:
- 1 refill (100 energy): +16 normal stages
- 3 refills (300 energy): +50 normal stages

---

## Balance Target Examples

### New Player (Levels 1-20)
```
Daily Income:
- Gold: 50,000
- Gems: 30 (F2P)

Characteristics:
- Fast progression (1.5× rate)
- Lower resource costs
- Tutorial rewards
- Beginner bonuses

Goal: Hook players, show progression
```

### Mid Player (Levels 21-50)
```
Daily Income:
- Gold: 150,000
- Gems: 100 (F2P)

Characteristics:
- Baseline progression (1.0× rate)
- Standard costs
- Full game loop active
- Balanced economy

Goal: Sustained engagement, monetization
```

### End Player (Level 51+)
```
Daily Income:
- Gold: 500,000
- Gems: 300 (F2P)

Characteristics:
- Slow progression (0.5× rate)
- High costs
- End game grind
- Prestige systems

Goal: Retention, long-term play
```

---

## Exchange Rate Examples

### Gem Conversions
```
1 Gem = 1,000 Gold
100 Gems = 100,000 Gold

1 Gem = 10 Energy
100 Gems = 1,000 Energy = 166 normal stages

Gold to Energy: Not available (prevents exploitation)
```

**Value Analysis**:
```
Normal Stage:
- Energy Cost: 6
- Gold Reward: 150

Gold per Energy: 150 / 6 = 25 gold/energy

Gem → Gold: 1 gem = 1,000 gold
Gem → Energy → Gold: 1 gem = 10 energy = 250 gold (via stages)

Conclusion: Direct conversion is 4× better than farming
```

---

## Inflation Control Examples

### Scenario 1: Excessive Gold Accumulation
```
Problem: Players accumulating too much gold (10M+ average)
Target: 2M average

Adjustments:
1. Increase upgrade costs by 20%
2. Add gold sinks (reroll systems, cosmetics)
3. Reduce gold drop rates by 10%

Result: Gradual deflation, economy stabilizes
```

### Scenario 2: Gem Shortage
```
Problem: F2P players can't afford gacha (< 500 gems)
Target: 1,500+ gems for retention

Adjustments:
1. Increase event gem rewards by 50%
2. Add daily gem missions
3. Improve achievement rewards

Result: Higher gem income, better retention
```

### Scenario 3: Energy Inflation
```
Problem: Players have excess energy (refills unused)
Target: 80% energy utilization

Adjustments:
1. Add high-energy content (raids, special stages)
2. Increase energy costs for premium rewards
3. Time-limited events requiring energy

Result: Higher energy usage, increased engagement
```

---

## Data Validation Rules

1. **Currency Limits**:
   - maxAmount must be > 0
   - maxAmount typically: 999,999 (event) to 999,999,999 (soft)
   - transferable: should be false (prevent real money trading)

2. **Source/Sink Balance**:
   - targetIncome > targetSpending (accumulation)
   - Typical surplus: 10-30% of income
   - Monitor actual vs. target divergence

3. **Resource Regeneration**:
   - rate must be > 0
   - interval: 60-600 seconds (1-10 minutes)
   - naturalCap ≤ maxAmount

4. **Exchange Rates**:
   - All rates must be > 0
   - gemToGold: 500-2000 typical range
   - gemToEnergy: 5-15 typical range

5. **Balance Targets**:
   - progressionRate: 0.5-2.0 reasonable range
   - dailyGold/Gems should increase with progression
   - Consistency across tiers

6. **Inflation Control**:
   - monthlyInflationTarget: 0.01-0.10 (1-10%)
   - adjustmentMethods: at least 2 methods recommended

---

## Design Guidelines

### Currency Design
- **Soft Currency**: Abundant, many sources, many sinks
- **Premium Currency**: Scarce, primarily IAP, high value
- **Event Currency**: Specific to mode/event, limited uses

### Resource Design
- **Energy**: Gate content, monetization lever, regenerates
- **Stamina**: Alternative energy system
- **Tickets**: Event-specific resources

### Balance Philosophy
- **New Players**: Fast progression, easy wins, hook engagement
- **Mid Players**: Balanced, sustainable, core loop
- **End Players**: Slow progression, prestige systems, retention focus

### Inflation Management
- **Monitor**: Track average player balances monthly
- **Target**: 3-5% monthly inflation acceptable
- **Adjust**: Use multiple levers (sources, sinks, conversion)

### Exchange Rates
- **Conservative**: Start with worse rates, improve later
- **Consistency**: Maintain rate ratios across systems
- **Fairness**: No exploitable conversions (arbitrage)

---

## Economic Monitoring

### Key Metrics
```
Currency:
- Average balance per player
- Daily income vs. spending
- Inflation rate (month-over-month)

Resources:
- Energy utilization rate (% used)
- Refill purchase rate
- Cap frequency (hitting max)

Conversion:
- Gem → Gold usage
- Gem → Energy usage
- Player preference ratios
```

### Health Indicators
```
Healthy Economy:
- 10-30% income surplus
- 80%+ energy utilization
- 3-5% monthly inflation
- Balanced currency distribution

Unhealthy Signs:
- Massive hoarding (10× target balance)
- Low utilization (< 50% energy used)
- Hyperinflation (> 10% monthly)
- Exploitable arbitrage
```

---

## Related Configuration Files

- **ShopProducts.json**: Uses currencies for purchases
- **Banners.json**: Consumes gems for gacha
- **Rewards.json**: Grants currencies/resources
- **Levels.json**: Stage energy costs and gold rewards
- **Quests.json**: Currency rewards from quests
- **ArenaConfig.json**: Arena coin rewards
- **SystemSettings.json**: Economy caps and limits

---

## Version History
- **1.0.0** (2025-11-23): Initial schema documentation
