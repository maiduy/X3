# GlobalModifiers.json Schema Documentation

## Overview
Defines temporary global modifiers, buffs, and special effects that can be activated server-side to modify game behavior without client updates. Used for events, promotions, emergency adjustments, and live operations.

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

### `activeModifiers` (array)
- **Type**: Array of Active Modifier Objects
- **Description**: Currently active modifiers affecting the game
- **Default**: `[]` (empty when no modifiers active)
- **Usage**: Activated modifiers from modifierTemplates with time bounds

### `modifierTemplates` (array)
- **Type**: Array of Modifier Template Objects
- **Description**: Predefined modifier configurations ready for activation
- **Required**: Yes

### `operationTypes` (object)
- **Type**: Operation Definition Map
- **Description**: Mathematical operations available for modifiers
- **Required**: Yes

### `scopeTypes` (object)
- **Type**: Scope Definition Map
- **Description**: Available targeting scopes for modifiers
- **Required**: Yes

### `activationRules` (object)
- **Type**: Global Activation Rules
- **Description**: System-wide rules for modifier activation and stacking
- **Required**: Yes

### `monitoring` (object)
- **Type**: Monitoring Configuration
- **Description**: Analytics and tracking settings for modifier impact
- **Required**: Yes

---

## Modifier Template Object

### `id` (string)
- **Format**: `MOD_[NAME]`
- **Example**: `"MOD_DOUBLE_GOLD"`, `"MOD_EXP_BOOST"`
- **Description**: Unique modifier identifier
- **Required**: Yes

### `meta` (object)
Modifier display and categorization metadata.

#### `nameKey` (string)
- **Type**: Localization Key
- **Description**: Modifier display name reference
- **Format**: `MOD_NAME_[MODIFIER_ID]`
- **Example**: `"MOD_NAME_DOUBLE_GOLD"`

#### `descriptionKey` (string)
- **Type**: Localization Key
- **Description**: Modifier description reference
- **Format**: `MOD_DESC_[MODIFIER_ID]`
- **Example**: `"MOD_DESC_DOUBLE_GOLD"`

#### `icon` (string)
- **Type**: Asset Reference
- **Description**: Modifier icon asset path
- **Example**: `"Icon_Modifier_DoubleGold"`

#### `category` (string)
- **Values**: `"Economy"`, `"Progression"`, `"Loot"`, `"PvP"`, `"Gacha"`, `"Combat"`, `"Raid"`
- **Description**: Modifier category for organization
- **Usage**: UI grouping and filtering

### `target` (string)
- **Description**: Game value this modifier affects
- **Common Values**:
  - `"DropRate_Gold"` - Gold drop rate multiplier
  - `"DropRate_All"` - All item drop rates
  - `"StaminaCost"` - Energy/stamina consumption
  - `"ExpGain"` - Experience point gain
  - `"PvPRewards"` - Arena/PvP rewards
  - `"GachaRate_SSR"` - SSR gacha rate
  - `"SkillCooldown"` - Skill cooldown duration
  - `"PlayerDamage"` - Player damage output
  - `"EnergyRefillCost"` - Energy refill cost
  - `"FirstClearRewards"` - First clear bonus rewards
- **Required**: Yes

### `operation` (string)
- **Values**: `"Add"`, `"Multiply"`, `"Set"`, `"Max"`, `"Min"`
- **Description**: Mathematical operation to apply
- **Required**: Yes

**Operation Details**:
- **Add**: `base + modifier` - Adds modifier value to base
- **Multiply**: `base * modifier` - Multiplies base by modifier
- **Set**: `modifier` - Overwrites base with modifier value
- **Max**: `max(base, modifier)` - Takes maximum value
- **Min**: `min(base, modifier)` - Takes minimum value

### `value` (number)
- **Type**: Float or Integer
- **Description**: Modifier value applied via operation
- **Examples**:
  - `2.0` for Multiply = double (200%)
  - `1.5` for Multiply = +50%
  - `0.5` for Multiply = half (50%)
  - `0.01` for Add = +1% (rate increase)
  - `50` for Add = +50 flat bonus
  - `0` for Set = free/zero cost
- **Required**: Yes

### `scope` (string)
- **Values**: `"Global"`, `"Region"`, `"Segment"`, `"Event"`, `"Raid"`, `"User"`
- **Description**: Targeting scope for modifier application
- **Required**: Yes

**Scope Descriptions**:
- **Global**: Applies to all players worldwide
- **Region**: Applies to specific regions only (requires regionFilter)
- **Segment**: Applies to specific user segments (requires segmentFilter)
- **Event**: Applies only during specific events (requires affectedEvents)
- **Raid**: Applies only in raid battles
- **User**: Applies to specific user IDs (requires userFilter)

### `stackable` (boolean)
- **Description**: Whether multiple instances can stack
- **Default**: `false`
- **Usage**: Prevents exploit from activating same modifier multiple times
- **Example**: Multiple EXP boosts = 1.5x + 1.5x + 1.5x if stackable

### `maxStacks` (number)
- **Type**: Integer
- **Description**: Maximum number of stacks if stackable
- **Optional**: Only required when stackable = true
- **Example**: `3` = maximum 3x this modifier can stack

### `affectedModes` (array)
- **Type**: Array of Mode Names
- **Description**: Game modes this modifier applies to
- **Optional**: If omitted, applies to all modes
- **Values**: `["PvE"]`, `["Arena", "RankedPvP"]`, `["Dungeon", "Raid"]`
- **Usage**: Restrict modifier to specific game modes

### `affectedEvents` (array)
- **Type**: Array of Event IDs
- **Description**: Specific events this modifier applies to
- **Optional**: For event-specific modifiers
- **Example**: `["EVENT_DRAGON_RAID"]`
- **Usage**: Boost specific raid event damage

### `affectedBanners` (array)
- **Type**: Array of Banner IDs
- **Description**: Specific gacha banners affected
- **Optional**: For gacha rate modifiers
- **Example**: `["BANNER_STANDARD"]`
- **Usage**: Rate-up events for specific banners

### `visual` (object)
Visual presentation configuration.

#### `showInUI` (boolean)
- **Description**: Display modifier in game UI
- **Default**: `true`
- **Usage**: Show active modifier badges/icons

#### `badgeIcon` (string)
- **Type**: Asset Reference
- **Description**: Badge icon displayed in UI
- **Example**: `"Badge_2xGold"`

#### `color` (string)
- **Type**: Hex Color Code
- **Description**: Badge/icon tint color
- **Format**: `#RRGGBB`
- **Example**: `"#FFD700"` (gold), `"#00FF00"` (green)
- **Optional**: If omitted, uses default color

#### `particleEffect` (string)
- **Type**: VFX Asset Reference
- **Description**: Particle effect to display
- **Example**: `"VFX_GoldShower"`, `"VFX_ItemRain"`
- **Optional**: For dramatic effect presentation

### `limits` (object)
Usage limits for modifier.

#### `maxRefillsPerPlayer` (number)
- **Type**: Integer
- **Description**: Maximum uses per player
- **Example**: `3` = 3 free refills per player
- **Optional**: For limited-use modifiers

#### `resetDaily` (boolean)
- **Description**: Reset limits at daily reset
- **Default**: `true`
- **Usage**: Daily free refill limits

### `disclaimer` (string)
- **Type**: Display Text
- **Description**: Legal/info text shown with modifier
- **Example**: `"Rate up event active! SSR rate increased by 1%"`
- **Optional**: For gacha rate disclosures
- **Usage**: Transparency requirements

---

## Active Modifier Object

Active modifiers are instances of modifier templates with time bounds.

### Structure
```json
{
  "modifierId": "MOD_DOUBLE_GOLD",
  "activatedAt": "2025-12-01T00:00:00Z",
  "expiresAt": "2025-12-08T23:59:59Z",
  "priority": 10,
  "metadata": {
    "activatedBy": "admin@game.com",
    "reason": "Weekend event",
    "eventId": "EVENT_DOUBLE_DROP"
  }
}
```

### `modifierId` (string)
- **Description**: Reference to modifier template
- **Example**: `"MOD_DOUBLE_GOLD"`
- **Required**: Yes

### `activatedAt` (string)
- **Type**: ISO 8601 DateTime
- **Description**: When modifier became active
- **Example**: `"2025-12-01T00:00:00Z"`
- **Required**: Yes

### `expiresAt` (string)
- **Type**: ISO 8601 DateTime
- **Description**: When modifier expires
- **Example**: `"2025-12-08T23:59:59Z"`
- **Required**: Yes

### `priority` (number)
- **Type**: Integer (1-100)
- **Description**: Conflict resolution priority
- **Default**: `10`
- **Usage**: Higher priority wins conflicts

### `metadata` (object)
- **Optional**: Tracking information

#### `activatedBy` (string)
- **Description**: Admin user who activated
- **Example**: `"admin@game.com"`

#### `reason` (string)
- **Description**: Activation reason
- **Example**: `"Weekend event"`

#### `eventId` (string)
- **Description**: Associated event ID
- **Example**: `"EVENT_DOUBLE_DROP"`

---

## Operation Types Object

### Add Operation
```json
{
  "description": "Adds the modifier value to the base value",
  "formula": "base + modifier"
}
```

**Use Cases**:
- Flat bonus rewards (+50 gold)
- Rate increases (+1% SSR chance)
- Damage boosts (+100 damage)

**Example**:
```
Base PvP Rewards: 100 gold
Modifier: +50 (Add)
Result: 150 gold
```

### Multiply Operation
```json
{
  "description": "Multiplies the base value by the modifier value",
  "formula": "base * modifier"
}
```

**Use Cases**:
- Percentage bonuses (2x gold, 1.5x EXP)
- Cost reductions (0.5x energy = half cost)
- Rate multipliers (1.3x drop rate)

**Example**:
```
Base Gold Drop: 1000
Modifier: 2.0 (Multiply)
Result: 2000 gold
```

### Set Operation
```json
{
  "description": "Sets the value to the modifier value, ignoring base",
  "formula": "modifier"
}
```

**Use Cases**:
- Free refills (set cost to 0)
- Fixed rewards (set to specific value)
- Override mechanics

**Example**:
```
Base Energy Refill Cost: 50 gems
Modifier: 0 (Set)
Result: 0 gems (free refill)
```

### Max Operation
```json
{
  "description": "Takes the maximum of base and modifier",
  "formula": "max(base, modifier)"
}
```

**Use Cases**:
- Minimum guarantees
- Floor values
- Safety caps

### Min Operation
```json
{
  "description": "Takes the minimum of base and modifier",
  "formula": "min(base, modifier)"
}
```

**Use Cases**:
- Maximum limits
- Ceiling values
- Balance caps

---

## Scope Types Object

### Scope Definitions
```json
{
  "Global": "Applies to all players worldwide",
  "Region": "Applies to specific regions only",
  "Segment": "Applies to specific user segments",
  "Event": "Applies only during specific events",
  "Raid": "Applies only in raid battles",
  "User": "Applies to specific user IDs"
}
```

**Usage Examples**:
- **Global**: Weekend double gold for all players
- **Region**: Asia-specific event bonuses
- **Segment**: New player welcome bonuses
- **Event**: Dragon raid damage boost
- **Raid**: Raid-only damage multiplier
- **User**: Compensation for specific affected users

---

## Activation Rules Object

### `maxActiveModifiers` (number)
- **Type**: Integer
- **Description**: Maximum concurrent active modifiers
- **Default**: `10`
- **Usage**: Prevent modifier spam/abuse

### `conflictResolution` (string)
- **Values**: `"HighestPriority"`, `"LastActivated"`, `"FirstActivated"`
- **Description**: How to resolve conflicting modifiers
- **Default**: `"HighestPriority"`

### `stackingRules` (object)

#### `sameType` (string)
- **Values**: `"Replace"`, `"Stack"`, `"Deny"`
- **Description**: Behavior when activating same modifier type
- **Default**: `"Replace"`

#### `differentType` (string)
- **Values**: `"Stack"`, `"Replace"`
- **Description**: Behavior for different modifier types
- **Default**: `"Stack"`

**Stacking Examples**:
```
Scenario 1: Same Type
- Active: MOD_EXP_BOOST (1.5x)
- Activate: MOD_EXP_BOOST (1.5x) again
- sameType: "Replace" → Only one 1.5x active
- sameType: "Stack" → 1.5x + 1.5x = 3.0x total

Scenario 2: Different Types
- Active: MOD_DOUBLE_GOLD (2x)
- Activate: MOD_EXP_BOOST (1.5x)
- differentType: "Stack" → Both active simultaneously
```

### `expiration` (object)

#### `autoExpire` (boolean)
- **Description**: Automatically deactivate when expired
- **Default**: `true`
- **Usage**: Cleanup expired modifiers

#### `notifyBeforeExpiration` (number)
- **Type**: Integer (seconds)
- **Description**: Notify players before expiration
- **Default**: `3600` (1 hour)
- **Usage**: Give advance warning to players

---

## Monitoring Object

### `trackUsage` (boolean)
- **Description**: Track modifier activation/deactivation
- **Default**: `true`
- **Usage**: Analytics for modifier effectiveness

### `trackImpact` (boolean)
- **Description**: Track modifier impact on metrics
- **Default**: `true`
- **Usage**: Measure effect on economy/engagement

### `metricsToTrack` (array)
- **Type**: Array of Metric IDs
- **Description**: Specific metrics to monitor
- **Examples**:
  - `"METRIC_CURRENCY_EARNED"` - Total currency gained
  - `"METRIC_CURRENCY_SPENT"` - Total currency spent
  - `"METRIC_GACHA_PULL_COUNT"` - Gacha pull volume
  - `"METRIC_STAGE_COMPLETION"` - Stage clear rate
- **Usage**: A/B testing and impact analysis

---

## Modifier Calculation Examples

### Example 1: Double Gold Event
```
Modifier: MOD_DOUBLE_GOLD
- Target: DropRate_Gold
- Operation: Multiply
- Value: 2.0

Stage Completion:
Base Gold Drop: 1,000
Modifier Applied: 1,000 * 2.0 = 2,000 gold
Player Receives: 2,000 gold
```

### Example 2: Half Energy Cost
```
Modifier: MOD_REDUCED_ENERGY_COST
- Target: StaminaCost
- Operation: Multiply
- Value: 0.5

Stage Entry:
Base Energy Cost: 20
Modifier Applied: 20 * 0.5 = 10 energy
Player Pays: 10 energy (50% discount)
```

### Example 3: Free Energy Refill
```
Modifier: MOD_FREE_ENERGY_REFILL
- Target: EnergyRefillCost
- Operation: Set
- Value: 0
- Limits: maxRefillsPerPlayer = 3, resetDaily = true

Refill Action:
Base Refill Cost: 50 gems
Modifier Applied: Set to 0
Player Pays: 0 gems
Remaining Free Refills: 2/3
```

### Example 4: SSR Rate Up
```
Modifier: MOD_SUMMON_RATE_UP
- Target: GachaRate_SSR
- Operation: Add
- Value: 0.01

Gacha Pull:
Base SSR Rate: 0.02 (2%)
Modifier Applied: 0.02 + 0.01 = 0.03 (3%)
Player SSR Chance: 3% (+1% bonus)
```

### Example 5: Stacked EXP Boost
```
Modifier: MOD_EXP_BOOST (stackable, maxStacks: 3)
- Target: ExpGain
- Operation: Multiply
- Value: 1.5

Active Stacks: 3
Stack Calculation (multiplicative):
Base EXP: 1,000
Stack 1: 1,000 * 1.5 = 1,500
Stack 2: 1,500 * 1.5 = 2,250
Stack 3: 2,250 * 1.5 = 3,375
Player Receives: 3,375 EXP (3.375x boost)

Alternative (additive stacking):
Base EXP: 1,000
Total Multiplier: 1.5 + 1.5 + 1.5 = 4.5
Player Receives: 4,500 EXP
```

### Example 6: Raid Damage Boost
```
Modifier: MOD_RAID_DMG_BOOST
- Target: PlayerDamage
- Operation: Multiply
- Value: 1.25
- Scope: Raid
- affectedEvents: ["EVENT_DRAGON_RAID"]

Raid Battle:
Base Player Damage: 10,000
Modifier Applied: 10,000 * 1.25 = 12,500
Final Damage: 12,500 (+25% boost)

Outside Raid:
Base Player Damage: 10,000
Modifier Not Applied (wrong scope)
Final Damage: 10,000 (no boost)
```

---

## Common Use Cases

### Weekend Events
```json
{
  "modifierId": "MOD_DOUBLE_GOLD",
  "activatedAt": "2025-12-06T00:00:00Z",
  "expiresAt": "2025-12-08T23:59:59Z",
  "priority": 10
}
```

### New Player Welcome Bonus
```json
{
  "id": "MOD_NEWBIE_BOOST",
  "scope": "Segment",
  "target": "ExpGain",
  "operation": "Multiply",
  "value": 2.0,
  "segmentFilter": ["NewPlayers"],
  "affectedModes": ["PvE"]
}
```

### Emergency Economy Adjustment
```json
{
  "id": "MOD_EMERGENCY_REFUND",
  "scope": "User",
  "target": "EnergyRefillCost",
  "operation": "Set",
  "value": 0,
  "userFilter": ["user123", "user456"],
  "limits": {
    "maxRefillsPerPlayer": 5,
    "resetDaily": false
  }
}
```

### Seasonal Gacha Event
```json
{
  "id": "MOD_HOLIDAY_RATE_UP",
  "scope": "Global",
  "target": "GachaRate_SSR",
  "operation": "Add",
  "value": 0.01,
  "affectedBanners": ["BANNER_HOLIDAY_2025"],
  "disclaimer": "Holiday Rate Up! SSR rate increased to 3%!"
}
```

---

## Activation Workflow

### Step 1: Activate Modifier
```
1. Select modifier template from modifierTemplates
2. Set activation time and expiration
3. Set priority for conflict resolution
4. Add to activeModifiers array
5. Push config update to server
```

### Step 2: Client Detection
```
1. Client fetches updated config
2. Detects new active modifier
3. Displays UI badge/notification
4. Applies modifier to gameplay calculations
```

### Step 3: Expiration
```
1. Server checks expiration timestamp
2. If expired and autoExpire = true:
   - Remove from activeModifiers
   - Send expiration notification (if notifyBeforeExpiration)
   - Update config
3. Client removes UI indicators
```

---

## Conflict Resolution Examples

### Scenario 1: Same Modifier, Replace Mode
```
Active Modifiers:
- MOD_DOUBLE_GOLD (priority 10, expires 2025-12-08)

Activate:
- MOD_DOUBLE_GOLD (priority 10, expires 2025-12-15)

Result (sameType: Replace):
- Remove first MOD_DOUBLE_GOLD
- Add new MOD_DOUBLE_GOLD with later expiration
```

### Scenario 2: Multiple Modifiers, Priority Conflict
```
Active Modifiers:
- MOD_EXP_BOOST (priority 10, 1.5x)

Activate:
- MOD_EXP_MEGA_BOOST (priority 20, 2.0x)
- Both target ExpGain

Result (HighestPriority):
- MOD_EXP_MEGA_BOOST takes precedence (priority 20)
- Player gets 2.0x EXP, not 1.5x
```

### Scenario 3: Compatible Modifiers, Stack
```
Active Modifiers:
- MOD_DOUBLE_GOLD (2x gold)

Activate:
- MOD_EXP_BOOST (1.5x EXP)

Result (differentType: Stack):
- Both active simultaneously
- Players get 2x gold AND 1.5x EXP
```

---

## Data Validation Rules

1. **Value Ranges**:
   - Multiply operations: value should be 0.0-5.0 (reasonable range)
   - Add operations: value should match target scale
   - Set operations: value ≥ 0

2. **Stacking Logic**:
   - If stackable = true, maxStacks must be specified
   - maxStacks should be 1-10 (prevent abuse)

3. **Scope Consistency**:
   - Region scope requires regionFilter
   - Segment scope requires segmentFilter
   - Event scope requires affectedEvents
   - User scope requires userFilter

4. **Time Bounds**:
   - activatedAt < expiresAt in active modifiers
   - Expiration should not exceed 30 days (reasonable limit)

5. **Priority Values**:
   - Priority should be 1-100
   - Higher priority = higher precedence

6. **Target Validity**:
   - Target should match known game values
   - Operation should be compatible with target type

7. **Max Active Modifiers**:
   - activeModifiers.length ≤ maxActiveModifiers

8. **Visual Requirements**:
   - If showInUI = true, badgeIcon should be specified

9. **Limit Logic**:
   - If limits specified, maxRefillsPerPlayer > 0
   - resetDaily requires time-based tracking

---

## Design Guidelines

### Economy Impact
- **Conservative Values**: Start with small multipliers (1.1x-1.5x)
- **Time Limits**: Short durations (24-72 hours) to create urgency
- **Frequency**: Don't activate too often (special occasions only)

### Player Communication
- **Advance Notice**: Announce modifiers before activation
- **Clear Disclaimers**: Especially for gacha rates
- **Expiration Warnings**: Notify 1 hour before expiration

### Balance Considerations
- **PvP Impacts**: Be cautious with combat modifiers in PvP
- **Economy Inflation**: Monitor currency gain/spend ratios
- **Player Expectations**: Consistent event patterns

### Technical Safety
- **Rollback Plan**: Be able to deactivate instantly
- **Monitoring**: Track metrics for unexpected behavior
- **Testing**: Test modifiers in staging environment first

### Stacking Strategy
- **Single Modifier Type**: Usually non-stackable
- **Different Categories**: Allow stacking (EXP + Gold)
- **Max Stacks**: Limit to 3 for stackable modifiers

---

## Related Configuration Files

- **EventSchedule.json**: Events that activate modifiers
- **FeatureFlags.json**: Feature gates that enable/disable systems
- **Rewards.json**: Drop tables affected by drop rate modifiers
- **SystemSettings.json**: Base values modified by operations
- **ArenaConfig.json**: PvP rewards affected by PvP modifiers
- **KillSwitch.json**: Emergency disable if modifier causes issues

---

## Version History
- **1.0.0** (2025-11-23): Initial schema documentation
