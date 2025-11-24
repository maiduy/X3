# FeatureFlags.json Schema Documentation

## Overview
Defines feature toggles, gradual rollouts, A/B testing, and kill switches for remote feature management without client updates.

---

## Root Level Fields

### `flags` (array)
Array of feature flag configurations for controlling feature availability.

### `abTests` (array)
A/B testing experiments with multiple variants and target audiences.

### `remoteConfig` (object)
Configuration for remote config system updates.

---

## Feature Flag Object

### `id` (string)
- **Format**: `FEATURE_[NAME]`
- **Example**: `"FEATURE_PVP_ARENA"`
- **Usage**: Unique feature identifier

### `meta` (object)

#### `name` (string)
- **Description**: Human-readable feature name
- **Example**: `"PvP Arena System"`

#### `description` (string)
- **Description**: Feature purpose and scope
- **Example**: `"Enable or disable the entire PvP arena feature"`

#### `category` (string)
- **Values**: `"GameMode"`, `"Social"`, `"Monetization"`, `"Economy"`, `"Gameplay"`, `"Analytics"`, `"LiveOps"`, `"Technical"`

### `enabled` (boolean)
- **Description**: Master toggle for feature
- **Default**: `false` for new features
- **Usage**: Can be toggled remotely

### `rollout` (object)
Gradual feature deployment configuration.

#### `strategy` (string)
- **Values**:
  - `"All"` - Available to everyone
  - `"Gradual"` - Percentage-based rollout
  - `"Beta"` - Limited to beta testers
  - `"RegionBased"` - By geographic region
  - `"None"` - Disabled for all

#### `percentage` (number)
- **Type**: Integer (0-100)
- **Description**: Percentage of users with access
- **Example**: `10` = 10% of players

#### `targetGroups` (array)
- **Type**: Array of Group Names
- **Values**: `["All"]`, `["BetaTesters"]`, `["VIP"]`, `["NewPlayers"]`
- **Usage**: Specific user segments

#### `excludedRegions` (array)
- **Type**: Array of Region IDs
- **Optional**: For region-based restrictions
- **Example**: `["CN"]`

### `requirements` (object)
Feature availability requirements.

#### `minVersion` (string)
- **Type**: Semantic Version
- **Description**: Minimum client version required
- **Example**: `"1.0.0"`

#### `platforms` (array)
- **Type**: Array of Platform Names
- **Values**: `["iOS", "Android", "PC", "Web"]`
- **Example**: `["iOS", "Android", "PC"]`

#### `regions` (array)
- **Type**: Array of Region IDs
- **Values**: `["Global"]`, `["Asia"]`, `["EU"]`, `["NA"]`, `["Except:CN"]`

### `config` (object)
- **Optional**: Feature-specific configuration
- **Example**:
```json
{
  "maxRefillsPerDay": 10,
  "costIncrement": true,
  "baseCost": 30
}
```

### `killSwitch` (object)
Emergency disable mechanism.

#### `enabled` (boolean)
- **Description**: Whether kill switch is available
- **Default**: `true` for production features

#### `reason` (string | null)
- **Description**: Why feature was disabled
- **Example**: `"Critical bug detected in matchmaking"`
- **Usage**: Display to internal team

---

## A/B Test Object

### `id` (string)
- **Format**: `AB_TEST_[NAME]`
- **Example**: `"AB_TEST_GACHA_RATES"`

### `meta` (object)

#### `name` (string)
- **Example**: `"Gacha Rate Test"`

#### `description` (string)
- **Example**: `"Test different SSR rates for new players"`

#### `startDate` (string)
- **Type**: ISO 8601 DateTime
- **Example**: `"2025-12-01T00:00:00Z"`

#### `endDate` (string)
- **Type**: ISO 8601 DateTime
- **Example**: `"2025-12-15T23:59:59Z"`

### `variants` (array)
Test variants with different configurations.

#### Variant Object:

##### `variant` (string)
- **Values**: `"Control"`, `"TestA"`, `"TestB"`, etc.
- **Description**: Variant identifier

##### `percentage` (number)
- **Type**: Integer (0-100)
- **Description**: Player distribution percentage
- **Total**: All variants must sum to 100

##### `config` (object)
- **Description**: Variant-specific parameters
- **Example**: `{"ssrRate": 0.03}`

### `targetAudience` (object)
Player segment for test.

#### `minLevel` (number)
- **Description**: Minimum player level
- **Example**: `1`

#### `maxLevel` (number)
- **Description**: Maximum player level
- **Example**: `10`

#### `newPlayersOnly` (boolean)
- **Description**: Restrict to new accounts
- **Default**: `false`

### `metrics` (array)
- **Type**: Array of Metric IDs
- **Description**: Success metrics to track
- **Examples**: `["METRIC_RETENTION_D7", "METRIC_CONVERSION_RATE", "METRIC_ARPU"]`

---

## Remote Config Object

### `updateFrequency` (number)
- **Type**: Integer (seconds)
- **Description**: How often to check for config updates
- **Default**: `300` (5 minutes)

### `cacheDuration` (number)
- **Type**: Integer (seconds)
- **Description**: How long to cache config locally
- **Default**: `3600` (1 hour)

### `fallbackToLocal` (boolean)
- **Description**: Use local config if remote fetch fails
- **Default**: `true`

---

## Common Use Cases

### Gradual Rollout
```
Day 1: 10% users
Day 3: 25% users
Day 7: 50% users
Day 14: 100% users (if no issues)
```

### Kill Switch Activation
```
1. Critical bug detected
2. Set enabled: false, reason: "Bug description"
3. Push config update
4. Feature disabled for all users within updateFrequency
```

### A/B Testing
```
Control Group (50%): ssrRate = 0.02
Test Group (50%): ssrRate = 0.03
Duration: 14 days
Metrics: D7 retention, conversion rate
Winner: Variant with better metrics
```

---

## Version History
- **1.0.0** (2025-11-23): Initial schema documentation
