# Analytics.json Schema Documentation

## Overview
Defines analytics metrics, data collection configuration, alert thresholds, dashboards, and export settings. Controls the game's telemetry system for monitoring engagement, monetization, economy, progression, PvP, and technical performance.

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

### `metrics` (array)
- **Type**: Array of Metric Objects
- **Description**: All analytics metric definitions
- **Required**: Yes

### `dashboards` (array)
- **Type**: Array of Dashboard Objects
- **Description**: Dashboard configurations for metric visualization
- **Required**: Yes

### `exportConfig` (object)
- **Description**: Data export configuration
- **Required**: Yes

---

## Metric Object

### `id` (string)
- **Format**: `METRIC_[NAME]`
- **Example**: `"METRIC_DAU"`, `"METRIC_ARPU"`, `"METRIC_RETENTION_D7"`
- **Description**: Unique metric identifier
- **Required**: Yes

### `meta` (object)
Metric metadata and categorization.

#### `name` (string)
- **Type**: Display Text
- **Description**: Human-readable metric name
- **Example**: `"Daily Active Users"`, `"Average Revenue Per User"`
- **Required**: Yes

#### `description` (string)
- **Type**: Display Text
- **Description**: Metric purpose and calculation method
- **Example**: `"Number of unique users who logged in during a 24-hour period"`
- **Required**: Yes

#### `category` (string)
- **Values**: `"Engagement"`, `"Retention"`, `"Monetization"`, `"Economy"`, `"Progression"`, `"PvP"`, `"Technical"`
- **Description**: Metric category for organization
- **Required**: Yes

**Category Descriptions**:
- **Engagement**: User activity, session metrics (DAU, MAU, session length)
- **Retention**: Player return behavior (D1, D7, D30 retention)
- **Monetization**: Revenue and conversion (ARPU, ARPPU, conversion rate)
- **Economy**: In-game economy health (currency flow, gacha pulls)
- **Progression**: Player advancement (stage completion, level up)
- **PvP**: Competitive mode participation and performance
- **Technical**: Performance and stability (crash rate, load time)

#### `priority` (string)
- **Values**: `"Critical"`, `"High"`, `"Medium"`, `"Low"`
- **Description**: Metric importance for business
- **Default**: `"Medium"`

**Priority Levels**:
- **Critical**: Key business metrics, require immediate attention (DAU, retention, ARPU, crash rate)
- **High**: Important but not critical (session length, conversion, participation)
- **Medium**: Informative metrics (gacha pulls, currency flow)
- **Low**: Nice-to-have tracking

---

## Collection Object

### `event` (string)
- **Description**: Game event that triggers metric collection
- **Example**: `"UserLogin"`, `"Purchase"`, `"GachaPull"`, `"SessionEnd"`
- **Required**: Yes

**Common Events**:
- **UserLogin**: Player authentication successful
- **SessionEnd**: Play session completed/terminated
- **Purchase**: IAP or in-game purchase made
- **FirstPurchase**: First-time purchase by user
- **GachaPull**: Gacha/summon performed
- **CurrencyGained**: Currency earned
- **CurrencySpent**: Currency consumed
- **StageEnd**: Stage/level completed or failed
- **ArenaMatch**: PvP match completed
- **SceneLoad**: Game scene loaded

### `frequency` (string)
- **Values**: `"RealTime"`, `"Daily"`, `"Hourly"`, `"Weekly"`
- **Description**: Data collection frequency
- **Required**: Yes

**Frequency Types**:
- **RealTime**: Collect immediately on event (highest accuracy, highest cost)
- **Daily**: Batch collection daily (good for retention, conversions)
- **Hourly**: Collect every hour (balance of accuracy and cost)
- **Weekly**: Weekly aggregation (low priority metrics)

### `dataType` (string)
- **Values**: `"Count"`, `"Duration"`, `"Percentage"`, `"Currency"`, `"Amount"`
- **Description**: Type of data being collected
- **Required**: Yes

**Data Types**:
- **Count**: Simple counting (DAU, pull count)
- **Duration**: Time measurements in milliseconds (session length, load time)
- **Percentage**: Ratio calculations (retention %, conversion %, crash rate)
- **Currency**: Monetary values (ARPU, revenue)
- **Amount**: Quantities (currency earned/spent)

### `aggregation` (string)
- **Values**: `"UniqueCount"`, `"Sum"`, `"Average"`, `"RetentionCohort"`, `"ConversionRate"`, `"CompletionRate"`, `"ParticipationRate"`, `"CrashRate"`, `"AveragePerUser"`, `"AveragePerPayingUser"`
- **Description**: How to aggregate collected data
- **Required**: Yes

**Aggregation Methods**:
- **UniqueCount**: Count unique entities (DAU, MAU)
- **Sum**: Total all values (total pulls, total currency)
- **Average**: Mean of values (avg session length, avg load time)
- **RetentionCohort**: Cohort analysis for retention
- **ConversionRate**: Percentage of conversions
- **CompletionRate**: Success rate calculation
- **ParticipationRate**: Engagement rate calculation
- **CrashRate**: Failure rate calculation
- **AveragePerUser**: Per-user average (ARPU)
- **AveragePerPayingUser**: Per-paying-user average (ARPPU)

### `dimensions` (array)
- **Type**: Array of Dimension Names
- **Description**: Data segmentation dimensions
- **Example**: `["Date", "Platform", "Region"]`
- **Required**: Yes

**Common Dimensions**:
- **Date/Month**: Time-based segmentation
- **Platform**: iOS, Android, PC, Web
- **Region**: Geographic segmentation
- **UserLevel**: Player level brackets
- **UserSegment**: Behavioral segments (whale, dolphin, F2P)
- **Source**: Acquisition source (organic, ads, referral)
- **InstallDate**: Cohort date
- **BannerId**: Gacha banner
- **StageId**: Stage identifier
- **Difficulty**: Content difficulty tier
- **CurrencyType**: Currency identifier
- **Sink/Source**: Economy flow type
- **Version**: App version
- **SceneName**: Game scene
- **Tier**: Arena tier

---

## Storage Object

### `database` (string)
- **Values**: `"MongoDB"`, `"PostgreSQL"`, `"BigQuery"`, `"Redshift"`
- **Description**: Database system for storage
- **Default**: `"MongoDB"`
- **Required**: Yes

### `collection` (string)
- **Description**: Database collection/table name
- **Example**: `"engagement_metrics"`, `"monetization_metrics"`
- **Required**: Yes

**Common Collections**:
- **engagement_metrics**: DAU, MAU, session metrics
- **retention_metrics**: D1, D7, D30 retention
- **monetization_metrics**: ARPU, ARPPU, conversion
- **economy_metrics**: Currency flow, gacha activity
- **progression_metrics**: Stage completion, level up
- **pvp_metrics**: Arena participation, win rates
- **technical_metrics**: Crash rate, load time

### `retentionDays` (number)
- **Type**: Integer (days)
- **Description**: How long to retain data
- **Example**: `30`, `90`, `180`, `365`, `730`
- **Required**: Yes
- **Usage**: Data lifecycle management, compliance

**Retention Guidelines**:
- **30 days**: Technical metrics (load time, low priority)
- **90 days**: Economy, progression (medium priority)
- **180 days**: PvP, progression (high priority)
- **365 days**: Engagement, retention (critical)
- **730 days**: Monetization (financial records)

---

## Alerts Object

### `enabled` (boolean)
- **Description**: Enable alerting for this metric
- **Default**: `false`
- **True**: Monitor and alert on threshold
- **False**: Track only, no alerts

### `threshold` (object)
Alert threshold configuration.

#### `type` (string)
- **Values**: `"Percentage"`, `"Absolute"`
- **Description**: Threshold type
- **Required**: When alerts enabled

**Threshold Types**:
- **Percentage**: Relative change (-10% = 10% decrease)
- **Absolute**: Fixed value (below 40% retention, above 2% crash rate)

#### `value` (number)
- **Type**: Integer or Float
- **Description**: Threshold value
- **Example**: `-10` (10% drop), `40` (40% absolute), `2` (2% crash rate)
- **Required**: When alerts enabled

**Value Interpretation**:
- **Percentage type**: Negative = decrease alert, Positive = increase alert
- **Absolute type**: Depends on comparison (below/above)

#### `comparison` (string)
- **Values**: `"DayOverDay"`, `"WeekOverWeek"`, `"MonthOverMonth"`, `"Below"`, `"Above"`
- **Description**: Comparison method
- **Required**: When alerts enabled

**Comparison Methods**:
- **DayOverDay**: Compare to previous day
- **WeekOverWeek**: Compare to same day last week
- **MonthOverMonth**: Compare to same day last month
- **Below**: Alert when value falls below threshold
- **Above**: Alert when value exceeds threshold

#### `scope` (string)
- **Values**: `"Global"`, `"PerPlatform"`, `"PerStage"`, `"PerSegment"`
- **Description**: Alert scope granularity
- **Optional**: Defaults to Global

---

## Dashboard Object

### `id` (string)
- **Format**: `DASHBOARD_[NAME]`
- **Example**: `"DASHBOARD_OVERVIEW"`, `"DASHBOARD_MONETIZATION"`
- **Description**: Unique dashboard identifier
- **Required**: Yes

### `name` (string)
- **Type**: Display Text
- **Description**: Dashboard display name
- **Example**: `"Game Overview"`, `"Monetization"`
- **Required**: Yes

### `metrics` (array)
- **Type**: Array of Metric IDs
- **Description**: Metrics displayed on this dashboard
- **Example**: `["METRIC_DAU", "METRIC_MAU", "METRIC_RETENTION_D1"]`
- **Required**: Yes

### `refreshInterval` (number)
- **Type**: Integer (seconds)
- **Description**: Dashboard auto-refresh interval
- **Example**: `300` (5 minutes), `600` (10 minutes)
- **Default**: `300`
- **Usage**: Real-time vs batch dashboards

---

## Export Config Object

### `enabled` (boolean)
- **Description**: Enable data export
- **Default**: `true`

### `format` (string)
- **Values**: `"CSV"`, `"JSON"`, `"Parquet"`, `"Avro"`
- **Description**: Export file format
- **Default**: `"CSV"`

### `schedule` (string)
- **Values**: `"Hourly"`, `"Daily"`, `"Weekly"`, `"Monthly"`
- **Description**: Export frequency
- **Default**: `"Daily"`

### `destination` (string)
- **Values**: `"S3"`, `"GCS"`, `"Azure"`, `"MongoDB"`, `"SFTP"`
- **Description**: Export destination
- **Default**: `"S3"`

### `compression` (boolean)
- **Description**: Compress exported files
- **Default**: `true`
- **True**: gzip compression
- **False**: Uncompressed

---

## Metric Examples

### DAU (Daily Active Users)
```json
{
  "id": "METRIC_DAU",
  "meta": {
    "name": "Daily Active Users",
    "category": "Engagement",
    "priority": "Critical"
  },
  "collection": {
    "event": "UserLogin",
    "frequency": "RealTime",
    "dataType": "Count",
    "aggregation": "UniqueCount",
    "dimensions": ["Date", "Platform", "Region"]
  },
  "alerts": {
    "enabled": true,
    "threshold": {
      "type": "Percentage",
      "value": -10,
      "comparison": "DayOverDay"
    }
  }
}
```

**Purpose**: Track active player base
**Alert**: Notify if DAU drops 10%+ vs yesterday
**Dimensions**: Segment by platform and region

### D7 Retention
```json
{
  "id": "METRIC_RETENTION_D7",
  "meta": {
    "name": "Day 7 Retention",
    "category": "Retention",
    "priority": "Critical"
  },
  "collection": {
    "event": "UserLogin",
    "frequency": "Daily",
    "dataType": "Percentage",
    "aggregation": "RetentionCohort",
    "dimensions": ["InstallDate", "Source", "Platform"]
  },
  "alerts": {
    "enabled": true,
    "threshold": {
      "type": "Absolute",
      "value": 20,
      "comparison": "Below"
    }
  }
}
```

**Purpose**: Measure 7-day player retention
**Calculation**: (Users returning on Day 7) / (Users installed on Day 0) × 100%
**Alert**: Notify if retention falls below 20%
**Cohort**: Track by install date and source

### ARPU (Average Revenue Per User)
```json
{
  "id": "METRIC_ARPU",
  "meta": {
    "name": "Average Revenue Per User",
    "category": "Monetization",
    "priority": "Critical"
  },
  "collection": {
    "event": "Purchase",
    "frequency": "Daily",
    "dataType": "Currency",
    "aggregation": "AveragePerUser",
    "dimensions": ["Date", "Platform", "UserSegment"]
  },
  "alerts": {
    "enabled": true,
    "threshold": {
      "type": "Percentage",
      "value": -15,
      "comparison": "WeekOverWeek"
    }
  }
}
```

**Purpose**: Track average revenue per user
**Calculation**: Total Revenue / Total Active Users
**Alert**: Notify if ARPU drops 15%+ vs last week
**Segments**: Compare whales, dolphins, F2P

### Crash Rate
```json
{
  "id": "METRIC_CRASH_RATE",
  "meta": {
    "name": "Crash Rate",
    "category": "Technical",
    "priority": "Critical"
  },
  "collection": {
    "event": "SessionEnd",
    "frequency": "RealTime",
    "dataType": "Percentage",
    "aggregation": "CrashRate",
    "dimensions": ["Date", "Platform", "Version"]
  },
  "alerts": {
    "enabled": true,
    "threshold": {
      "type": "Absolute",
      "value": 2,
      "comparison": "Above"
    }
  }
}
```

**Purpose**: Monitor app stability
**Calculation**: (Crashed Sessions) / (Total Sessions) × 100%
**Alert**: Notify if crash rate exceeds 2%
**Segments**: Platform and app version

---

## Dashboard Examples

### Game Overview Dashboard
```json
{
  "id": "DASHBOARD_OVERVIEW",
  "name": "Game Overview",
  "metrics": [
    "METRIC_DAU",
    "METRIC_MAU",
    "METRIC_RETENTION_D1",
    "METRIC_RETENTION_D7",
    "METRIC_ARPU",
    "METRIC_CONVERSION_RATE"
  ],
  "refreshInterval": 300
}
```

**Purpose**: Executive summary of key metrics
**Refresh**: Every 5 minutes
**Audience**: Management, product team

### Monetization Dashboard
```json
{
  "id": "DASHBOARD_MONETIZATION",
  "name": "Monetization",
  "metrics": [
    "METRIC_ARPU",
    "METRIC_ARPPU",
    "METRIC_CONVERSION_RATE",
    "METRIC_GACHA_PULL_COUNT"
  ],
  "refreshInterval": 600
}
```

**Purpose**: Track revenue and monetization health
**Refresh**: Every 10 minutes
**Audience**: Monetization team, product

### Economy Dashboard
```json
{
  "id": "DASHBOARD_ECONOMY",
  "name": "Economy Balance",
  "metrics": [
    "METRIC_CURRENCY_EARNED",
    "METRIC_CURRENCY_SPENT",
    "METRIC_GACHA_PULL_COUNT"
  ],
  "refreshInterval": 300
}
```

**Purpose**: Monitor in-game economy health
**Refresh**: Every 5 minutes
**Audience**: Economy designers, product

---

## Alert Examples

### DAU Drop Alert
```
Metric: METRIC_DAU
Threshold: -10% DayOverDay

Trigger Scenario:
Yesterday: 10,000 DAU
Today: 8,900 DAU
Change: -11% → ALERT TRIGGERED

Alert Message:
"DAU dropped 11% (10,000 → 8,900).
Investigate: server issues, holidays, competition launch?"
```

### D7 Retention Alert
```
Metric: METRIC_RETENTION_D7
Threshold: Below 20% Absolute

Trigger Scenario:
Cohort (Nov 1): 1,000 installs
Day 7 (Nov 8): 180 returned
Retention: 18% → ALERT TRIGGERED

Alert Message:
"D7 retention below target (18% < 20%).
Check: onboarding flow, new player experience, content gates."
```

### ARPU Drop Alert
```
Metric: METRIC_ARPU
Threshold: -15% WeekOverWeek

Trigger Scenario:
Last Week: $2.50 ARPU
This Week: $2.00 ARPU
Change: -20% → ALERT TRIGGERED

Alert Message:
"ARPU dropped 20% ($2.50 → $2.00).
Investigate: pricing changes, event fatigue, reduced offers?"
```

### Crash Rate Alert
```
Metric: METRIC_CRASH_RATE
Threshold: Above 2% Absolute

Trigger Scenario:
Total Sessions: 50,000
Crashed: 1,200
Crash Rate: 2.4% → ALERT TRIGGERED

Alert Message:
"Crash rate exceeded 2% (2.4%).
Platform: Android
Version: 1.5.2
Action: Review crash logs immediately."
```

### Stage Completion Alert
```
Metric: METRIC_STAGE_COMPLETION
Threshold: Below 30% Absolute (PerStage)

Trigger Scenario:
Stage: STAGE_3_5 (Boss)
Attempts: 1,000
Completions: 250
Rate: 25% → ALERT TRIGGERED

Alert Message:
"Stage 3-5 completion below 30% (25%).
Possible issue: overtuned difficulty, bug, poor player power."
```

---

## Metric Calculation Examples

### DAU Calculation
```
Day 1:
- User A: Login at 00:30
- User B: Login at 08:00
- User C: Login at 15:00
- User A: Login again at 23:00 (same user, don't double count)

DAU = 3 (unique users: A, B, C)
```

### MAU Calculation
```
November 2025:
- Week 1: 10,000 unique users
- Week 2: 12,000 unique users (8,000 same, 4,000 new)
- Week 3: 11,000 unique users (7,000 same, 4,000 new)
- Week 4: 13,000 unique users (9,000 same, 4,000 new)

MAU = 22,000 (total unique across month)
```

### D7 Retention Calculation
```
Cohort: Installed on Nov 1
- Day 0 (Nov 1): 1,000 installs
- Day 1 (Nov 2): 600 returned (60% D1)
- Day 7 (Nov 8): 200 returned (20% D7)

D7 Retention = (200 / 1,000) × 100% = 20%
```

### ARPU Calculation
```
Day 1:
- Total Revenue: $5,000
- Total Active Users: 10,000
- ARPU = $5,000 / 10,000 = $0.50

Month:
- Total Revenue: $150,000
- Total Active Users: 50,000
- ARPU = $150,000 / 50,000 = $3.00
```

### ARPPU Calculation
```
Day 1:
- Total Revenue: $5,000
- Total Paying Users: 500
- ARPPU = $5,000 / 500 = $10.00

(Note: ARPPU > ARPU because only paying users counted)
```

### Conversion Rate Calculation
```
Month:
- Total Users: 10,000
- Paying Users: 500
- Conversion Rate = (500 / 10,000) × 100% = 5%
```

### Crash Rate Calculation
```
Day 1:
- Total Sessions: 50,000
- Crashed Sessions: 1,000
- Crash Rate = (1,000 / 50,000) × 100% = 2%
```

---

## Data Validation Rules

1. **Metric IDs**:
   - Must be unique across all metrics
   - Format: METRIC_[NAME]

2. **Collection Frequency**:
   - RealTime: Highest cost, use sparingly
   - Daily: Most common, good balance
   - Match frequency to metric importance

3. **Data Retention**:
   - retentionDays: 30-730 days
   - Financial data: 730 days minimum (compliance)
   - Technical data: 30-90 days sufficient

4. **Alert Thresholds**:
   - Percentage: -100 to +100 (reasonable range)
   - Absolute: Must make sense for metric (2% crash, 40% retention)
   - comparison: Must match threshold type

5. **Dashboard Metrics**:
   - 3-8 metrics per dashboard (not too crowded)
   - refreshInterval: 60-3600 seconds
   - Group related metrics

6. **Dimensions**:
   - At least 1 dimension required
   - Limit to 5 dimensions (prevents data explosion)
   - Common dimensions: Date, Platform, Region

7. **Priority Consistency**:
   - Critical metrics should have alerts enabled
   - Low priority metrics: alerts disabled

---

## Design Guidelines

### Metric Selection
- **Start Small**: 10-15 core metrics initially
- **Add Gradually**: Expand as needs identified
- **Avoid Redundancy**: Don't track same thing multiple ways

### Alert Strategy
- **Critical Only**: Alert on business-critical metrics
- **Actionable**: Only alert if action can be taken
- **Avoid Fatigue**: Too many alerts = ignored alerts
- **Test Thresholds**: Tune to avoid false positives

### Dashboard Design
- **Role-Based**: Different dashboards for different teams
- **Focused**: 5-8 metrics per dashboard maximum
- **Hierarchy**: Overview → Detailed dashboards

### Data Retention
- **Compliance**: Follow legal requirements (financial: 2+ years)
- **Cost**: Storage costs increase with retention
- **Value**: Balance cost vs historical analysis value

### Performance
- **Sampling**: Sample high-volume events (10-50%)
- **Batching**: Batch write for efficiency
- **Indexing**: Index dimensions for query performance

---

## Common Metrics by Category

### Engagement
```
- METRIC_DAU (Daily Active Users)
- METRIC_MAU (Monthly Active Users)
- METRIC_SESSION_LENGTH (Avg session duration)
- METRIC_SESSION_COUNT (Sessions per user)
```

### Retention
```
- METRIC_RETENTION_D1 (Day 1 retention)
- METRIC_RETENTION_D7 (Day 7 retention)
- METRIC_RETENTION_D30 (Day 30 retention)
- METRIC_CHURN_RATE (User churn rate)
```

### Monetization
```
- METRIC_ARPU (Average revenue per user)
- METRIC_ARPPU (Average revenue per paying user)
- METRIC_CONVERSION_RATE (% paying users)
- METRIC_LTV (Lifetime value)
```

### Economy
```
- METRIC_CURRENCY_EARNED (Currency gained)
- METRIC_CURRENCY_SPENT (Currency consumed)
- METRIC_GACHA_PULL_COUNT (Total pulls)
- METRIC_CURRENCY_BALANCE (Average balance)
```

### Progression
```
- METRIC_STAGE_COMPLETION (Stage clear rate)
- METRIC_LEVEL_UP_RATE (Leveling speed)
- METRIC_POWER_GROWTH (Power progression)
- METRIC_QUEST_COMPLETION (Quest clear rate)
```

### PvP
```
- METRIC_ARENA_PARTICIPATION (% playing PvP)
- METRIC_ARENA_WIN_RATE (PvP win rate)
- METRIC_ARENA_MATCHES (Total matches)
- METRIC_RANK_DISTRIBUTION (Tier distribution)
```

### Technical
```
- METRIC_CRASH_RATE (App crash rate)
- METRIC_LOAD_TIME (Scene load time)
- METRIC_FPS (Frames per second)
- METRIC_MEMORY_USAGE (Memory consumption)
```

---

## Industry Benchmarks

### Engagement
```
Good DAU/MAU Ratio: 20-30%
Good Session Length: 10-30 minutes
Good Sessions/Day: 2-4
```

### Retention
```
Good D1 Retention: 40-50%
Good D7 Retention: 20-25%
Good D30 Retention: 10-15%
```

### Monetization
```
Good Conversion Rate: 2-5%
Good ARPU: $1-5 per user
Good ARPPU: $20-50 per paying user
```

### Technical
```
Acceptable Crash Rate: < 2%
Good Load Time: < 3 seconds
Good FPS: 30-60 fps
```

---

## Related Configuration Files

- **SegmentConfig.json**: User segments for dimension analysis
- **EventSchedule.json**: Events affecting engagement metrics
- **Economy.json**: Currency targets for economy metrics
- **Banners.json**: Gacha activity metrics
- **ArenaConfig.json**: PvP participation metrics
- **SystemSettings.json**: Performance targets

---

## Version History
- **1.0.0** (2025-11-23): Initial schema documentation
