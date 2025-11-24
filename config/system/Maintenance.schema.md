# Maintenance.json Schema Documentation

## Overview
Defines scheduled and emergency maintenance configurations, compensation systems, and maintenance health monitoring. Provides structured approach to planned downtime with player communication and automatic compensation.

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

### `maintenanceSchedule` (object)
- **Description**: Planned maintenance scheduling configuration
- **Required**: Yes
- **Usage**: Schedule future maintenance windows

### `currentMaintenance` (object)
- **Description**: Active maintenance status and details
- **Required**: Yes
- **Usage**: Track ongoing maintenance

### `compensation` (object)
- **Description**: Player compensation configuration
- **Required**: Yes
- **Usage**: Automatic rewards for downtime

### `monitoring` (object)
- **Description**: Maintenance health monitoring settings
- **Required**: Yes
- **Usage**: Automated issue detection

---

## Maintenance Schedule Object

### `isScheduled` (boolean)
- **Description**: Whether maintenance is scheduled
- **Default**: `false`
- **Usage**: Set to `true` when planning maintenance
- **Effect**: Triggers advance player notifications

### `plannedStart` (string | null)
- **Type**: ISO 8601 DateTime or null
- **Description**: Scheduled maintenance start time
- **Example**: `"2025-12-01T02:00:00Z"`
- **Null**: No maintenance scheduled
- **Required**: When isScheduled = true

### `plannedEnd` (string | null)
- **Type**: ISO 8601 DateTime or null
- **Description**: Scheduled maintenance end time
- **Example**: `"2025-12-01T06:00:00Z"`
- **Null**: No maintenance scheduled
- **Required**: When isScheduled = true

### `estimatedDuration` (number)
- **Type**: Integer (seconds)
- **Description**: Expected maintenance duration
- **Default**: `7200` (2 hours)
- **Usage**: Display countdown to players
- **Example**: `7200` = 2 hours, `14400` = 4 hours

### `notifyBeforeStart` (number)
- **Type**: Integer (seconds)
- **Description**: Advance notification time before start
- **Default**: `3600` (1 hour)
- **Usage**: Send push notifications X seconds before
- **Example**: `3600` = 1 hour notice, `86400` = 24 hour notice

### `allowEarlyEntry` (boolean)
- **Description**: Allow players to log in if maintenance completes early
- **Default**: `false`
- **Usage**: Flexibility for early completion
- **True**: Players can enter immediately when done
- **False**: Wait until plannedEnd even if done early

---

## Current Maintenance Object

### `isActive` (boolean)
- **Description**: Whether maintenance is currently active
- **Default**: `false`
- **Usage**: Set to `true` when maintenance begins
- **Effect**: Blocks player logins (except whitelisted)

### `startTime` (string | null)
- **Type**: ISO 8601 DateTime or null
- **Description**: When current maintenance started
- **Example**: `"2025-12-01T02:00:00Z"`
- **Null**: No active maintenance
- **Usage**: Calculate actual duration

### `endTime` (string | null)
- **Type**: ISO 8601 DateTime or null
- **Description**: When current maintenance ended
- **Example**: `"2025-12-01T05:30:00Z"`
- **Null**: Maintenance ongoing or not started
- **Usage**: Calculate compensation duration

### `type` (string)
- **Values**: `"Scheduled"`, `"Emergency"`, `"Hotfix"`, `"Extended"`
- **Description**: Type of maintenance
- **Default**: `"Scheduled"`

**Type Descriptions**:
- **Scheduled**: Planned regular maintenance (patches, updates)
- **Emergency**: Unplanned critical issues (server crash, exploit)
- **Hotfix**: Quick fix for urgent issues (usually < 1 hour)
- **Extended**: Scheduled maintenance taking longer than planned

### `severity` (string)
- **Values**: `"Normal"`, `"Critical"`, `"Minor"`
- **Description**: Maintenance severity level
- **Default**: `"Normal"`

**Severity Descriptions**:
- **Normal**: Standard scheduled maintenance
- **Critical**: Emergency maintenance for critical issues
- **Minor**: Small hotfix or update

### `reason` (string)
- **Type**: Display Text
- **Description**: Maintenance reason shown to players
- **Example**: `"Regular maintenance"`, `"Database migration"`, `"Critical bug fix"`
- **Required**: Yes
- **Usage**: Player communication

### `affectedServices` (array)
- **Type**: Array of Service Names
- **Description**: Which services are affected
- **Example**: `["PvP", "Gacha", "Shop"]`
- **Default**: `[]` (empty = all services affected)
- **Usage**: Partial maintenance scenarios

**Common Service Names**:
- `"PvP"` - Arena/ranked battles
- `"PvE"` - Campaign/dungeon stages
- `"Gacha"` - Summoning system
- `"Shop"` - Store and IAP
- `"Social"` - Friends/guilds/chat
- `"Login"` - Authentication (full maintenance)

---

## Compensation Object

### `enabled` (boolean)
- **Description**: Whether compensation is enabled
- **Default**: `true`
- **Usage**: Automatically give rewards after maintenance
- **Effect**: Players receive compensation via delivery method

### `autoCalculate` (boolean)
- **Description**: Automatically calculate compensation based on duration
- **Default**: `true`
- **Usage**: Scale rewards with downtime length
- **True**: Calculate based on formulas
- **False**: Use fixed baseRewards

### `baseRewards` (array)
- **Type**: Array of Reward Objects
- **Description**: Base compensation rewards
- **Structure**: `[{"itemId": "...", "amount": number}]`
- **Example**: `[{"itemId": "CURRENCY_GEM", "amount": 100}]`
- **Usage**: Minimum compensation or fixed amount

**Reward Object Structure**:
```json
{
  "itemId": "CURRENCY_GEM",
  "amount": 100
}
```

**Common Compensation Items**:
- `CURRENCY_GEM` - Premium currency (50-500)
- `CURRENCY_GOLD` - Soft currency (10,000-50,000)
- `ITEM_ENERGY_FULL` - Full energy refill (1-3)
- `ITEM_SUMMON_SCROLL` - Gacha scroll (1-10)
- `ITEM_EXP_POTION_LARGE` - Experience potion (5-20)

### `deliveryMethod` (string)
- **Values**: `"Mailbox"`, `"AutoClaim"`, `"LoginReward"`
- **Description**: How compensation is delivered
- **Default**: `"Mailbox"`

**Delivery Methods**:
- **Mailbox**: Sent to in-game mailbox
- **AutoClaim**: Automatically added to inventory on login
- **LoginReward**: Shown as login bonus popup

### `expirationDays` (number)
- **Type**: Integer (days)
- **Description**: Days until compensation expires
- **Default**: `7`
- **Usage**: Mailbox item expiration
- **Example**: `7` = must claim within 7 days

---

## Monitoring Object

### `healthCheckInterval` (number)
- **Type**: Integer (seconds)
- **Description**: Interval between health checks
- **Default**: `60` (1 minute)
- **Usage**: Automated monitoring frequency
- **Example**: `60` = check every minute, `300` = check every 5 minutes

### `autoDetectIssues` (boolean)
- **Description**: Automatically detect server issues
- **Default**: `true`
- **Usage**: Emergency maintenance trigger
- **Effect**: System can trigger emergency maintenance automatically

**Auto-Detection Scenarios**:
- Database connection failure
- API response time > threshold
- Error rate spike > 5%
- Server crash/restart
- Critical service failure

---

## Maintenance Scheduling Workflow

### Step 1: Schedule Maintenance
```json
{
  "maintenanceSchedule": {
    "isScheduled": true,
    "plannedStart": "2025-12-01T02:00:00Z",
    "plannedEnd": "2025-12-01T06:00:00Z",
    "estimatedDuration": 14400,
    "notifyBeforeStart": 86400,
    "allowEarlyEntry": false
  }
}
```

**Timeline**:
- **T-24h**: Schedule set, notifications sent
- **T-1h**: Reminder notifications
- **T-10m**: Final warning in-game
- **T-0**: Maintenance begins

### Step 2: Activate Maintenance
```json
{
  "maintenanceSchedule": {
    "isScheduled": false
  },
  "currentMaintenance": {
    "isActive": true,
    "startTime": "2025-12-01T02:00:00Z",
    "endTime": null,
    "type": "Scheduled",
    "severity": "Normal",
    "reason": "Database migration and patch deployment",
    "affectedServices": []
  }
}
```

**Actions**:
- Block player logins
- Graceful player disconnection
- Display maintenance message
- Begin maintenance tasks

### Step 3: Complete Maintenance
```json
{
  "currentMaintenance": {
    "isActive": false,
    "startTime": "2025-12-01T02:00:00Z",
    "endTime": "2025-12-01T05:30:00Z",
    "type": "Scheduled",
    "severity": "Normal",
    "reason": "Database migration and patch deployment",
    "affectedServices": []
  }
}
```

**Actions**:
- Allow player logins
- Calculate compensation
- Send compensation to all players
- Post-maintenance monitoring

### Step 4: Compensation Calculation
```
Actual Duration: 3.5 hours (5:30 AM - 2:00 AM)
Planned Duration: 4 hours

Auto-Calculate Compensation:
- Base: 100 gems (from baseRewards)
- Bonus: 50 gems per hour × 3.5 hours = 175 gems
- Total: 275 gems

Delivery:
- Method: Mailbox
- Expiration: 7 days
- Message: "Thank you for your patience during maintenance!"
```

---

## Maintenance Type Examples

### Scheduled Maintenance
```json
{
  "type": "Scheduled",
  "severity": "Normal",
  "reason": "Weekly server maintenance and content update",
  "estimatedDuration": 7200,
  "compensation": {
    "baseRewards": [
      {"itemId": "CURRENCY_GEM", "amount": 100},
      {"itemId": "ITEM_ENERGY_FULL", "amount": 2}
    ]
  }
}
```

**Typical Schedule**:
- **Frequency**: Weekly (Tuesday 2-6 AM UTC)
- **Duration**: 2-4 hours
- **Advance Notice**: 24 hours
- **Compensation**: Standard (100 gems + energy)

### Emergency Maintenance
```json
{
  "type": "Emergency",
  "severity": "Critical",
  "reason": "Critical exploit fix and rollback",
  "estimatedDuration": 3600,
  "compensation": {
    "autoCalculate": true,
    "baseRewards": [
      {"itemId": "CURRENCY_GEM", "amount": 300},
      {"itemId": "ITEM_SUMMON_SCROLL", "amount": 5}
    ]
  }
}
```

**Characteristics**:
- **No Advance Notice**: Immediate
- **Duration**: Variable (1-8 hours)
- **Compensation**: Higher (300+ gems)
- **Urgency**: Critical bugs, exploits, server crashes

### Hotfix Maintenance
```json
{
  "type": "Hotfix",
  "severity": "Minor",
  "reason": "Quick fix for display bug",
  "estimatedDuration": 600,
  "compensation": {
    "enabled": false
  }
}
```

**Characteristics**:
- **Duration**: < 30 minutes
- **Advance Notice**: 5-10 minutes
- **Compensation**: Usually none (too short)
- **Scope**: Minor fixes only

### Extended Maintenance
```json
{
  "type": "Extended",
  "severity": "Normal",
  "reason": "Scheduled maintenance extended due to technical issues",
  "estimatedDuration": 21600,
  "compensation": {
    "autoCalculate": true,
    "baseRewards": [
      {"itemId": "CURRENCY_GEM", "amount": 200},
      {"itemId": "ITEM_SUMMON_SCROLL", "amount": 10},
      {"itemId": "ITEM_ENERGY_FULL", "amount": 5}
    ]
  }
}
```

**Characteristics**:
- **Original**: 4 hours planned
- **Extended**: 6 hours actual
- **Compensation**: Enhanced (for inconvenience)
- **Communication**: Update notifications every hour

---

## Compensation Calculation Formulas

### Auto-Calculate Mode (Enabled)

#### Formula 1: Duration-Based
```
Maintenance Duration (hours) = (endTime - startTime) / 3600

Base Gems = baseRewards.gems
Bonus Gems = Duration × GemsPerHour

Total Gems = Base Gems + Bonus Gems

Constants:
- GemsPerHour = 50 (configurable)
- MaxBonus = 500 gems (cap)
```

**Example**:
```
Duration: 5 hours
Base: 100 gems
Bonus: 5 × 50 = 250 gems
Total: 350 gems
```

#### Formula 2: Severity-Based
```
Severity Multiplier:
- Minor: 1.0x
- Normal: 1.5x
- Critical: 2.0x

Total Gems = Base Gems × Severity Multiplier × Duration Factor
```

**Example**:
```
Type: Emergency
Severity: Critical
Duration: 3 hours
Base: 100 gems

Calculation:
Duration Factor: 3 / 2 = 1.5 (normalize to 2-hour base)
Total: 100 × 2.0 × 1.5 = 300 gems
```

### Auto-Calculate Mode (Disabled)
```
Fixed Compensation = baseRewards
No duration scaling
```

---

## Partial Maintenance Scenarios

### Scenario 1: PvP Only Maintenance
```json
{
  "currentMaintenance": {
    "isActive": true,
    "type": "Hotfix",
    "severity": "Minor",
    "reason": "Fixing PvP matchmaking issue",
    "affectedServices": ["PvP"]
  }
}
```

**Effect**:
- PvP/Arena disabled
- PvE/Gacha/Shop/Social all available
- Reduced or no compensation (partial downtime)

### Scenario 2: Shop & IAP Maintenance
```json
{
  "currentMaintenance": {
    "isActive": true,
    "type": "Scheduled",
    "severity": "Normal",
    "reason": "Shop system update and new product deployment",
    "affectedServices": ["Shop", "IAP"]
  }
}
```

**Effect**:
- Can't purchase items or IAP
- All gameplay features available
- Minimal compensation (shop-only)

### Scenario 3: Database Migration (Full)
```json
{
  "currentMaintenance": {
    "isActive": true,
    "type": "Scheduled",
    "severity": "Normal",
    "reason": "Database migration and optimization",
    "affectedServices": []
  }
}
```

**Effect**:
- Complete server downtime
- No login possible
- Full compensation

---

## Notification Timeline Examples

### Standard Scheduled Maintenance

#### 7 Days Before
```
In-game announcement banner:
"Scheduled maintenance on Dec 1, 2-6 AM UTC"
```

#### 24 Hours Before
```
Push notification:
"Server maintenance in 24 hours. Please plan accordingly."

In-game popup on login:
"Maintenance scheduled for Dec 1, 2-6 AM UTC (4 hours)"
```

#### 1 Hour Before
```
Push notification:
"Server maintenance starting in 1 hour!"

In-game banner (red):
"MAINTENANCE IN 60 MINUTES"
```

#### 10 Minutes Before
```
In-game modal (cannot dismiss):
"Server maintenance starting in 10 minutes. Please finish your activities."

Auto-save progress
Disable new battle starts
```

#### Maintenance Start
```
Login blocked:
"Server is under maintenance. Estimated completion: 6:00 AM UTC"

Redirect to maintenance page
```

#### Maintenance Complete
```
Push notification:
"Maintenance complete! Login now to claim your compensation rewards."

Mailbox item:
"Maintenance Compensation: 100 gems, 2 Energy Refills"
```

---

## Emergency Maintenance Response

### Detection Phase
```
System detects critical issue:
- Auto-detection triggered
- Operations team alerted
- Severity assessment
```

### Immediate Response
```json
{
  "currentMaintenance": {
    "isActive": true,
    "startTime": "2025-12-01T14:35:00Z",
    "type": "Emergency",
    "severity": "Critical",
    "reason": "Critical server issue detected",
    "affectedServices": []
  }
}
```

**Actions**:
1. Immediate login block
2. Graceful player disconnection (save progress)
3. Emergency notification sent
4. Status page updated

### Communication
```
Push notification (immediate):
"Emergency maintenance in progress. We apologize for the inconvenience."

In-game message:
"The server is experiencing technical difficulties. Our team is working to resolve the issue as quickly as possible."

Social media update:
"We are aware of the issue and are working on a fix. Updates will be provided every 30 minutes."
```

### Resolution
```json
{
  "currentMaintenance": {
    "isActive": false,
    "endTime": "2025-12-01T16:45:00Z",
    "type": "Emergency",
    "severity": "Critical",
    "reason": "Critical server issue - RESOLVED"
  },
  "compensation": {
    "enabled": true,
    "autoCalculate": true,
    "baseRewards": [
      {"itemId": "CURRENCY_GEM", "amount": 300},
      {"itemId": "ITEM_SUMMON_SCROLL", "amount": 10},
      {"itemId": "ITEM_ENERGY_FULL", "amount": 5}
    ]
  }
}
```

**Duration**: 2 hours 10 minutes
**Compensation**: Enhanced due to emergency nature

---

## Monitoring and Auto-Detection

### Health Check Metrics

#### Database Health
```
Check: Database connection
Interval: Every 60 seconds
Threshold: 3 consecutive failures
Action: Trigger emergency maintenance
```

#### API Response Time
```
Check: Average API response time
Interval: Every 60 seconds
Threshold: > 5000ms for 5 minutes
Action: Alert operations team, prepare maintenance
```

#### Error Rate
```
Check: Server error rate
Interval: Every 60 seconds
Threshold: > 5% error rate for 3 minutes
Action: Trigger emergency maintenance
```

#### Active Connections
```
Check: Active player connections
Interval: Every 60 seconds
Threshold: Sudden drop > 50%
Action: Investigation, possible maintenance
```

### Auto-Detection Flow
```
1. Health check fails threshold
2. Verify issue (not false positive)
3. Assess severity (Minor/Normal/Critical)
4. If Critical:
   - Auto-trigger emergency maintenance
   - Set currentMaintenance.isActive = true
   - Notify operations team
   - Begin automated mitigation
5. If Normal:
   - Alert operations team
   - Manual decision on maintenance
6. If Minor:
   - Log issue
   - Monitor closely
```

---

## Data Validation Rules

1. **Time Consistency**:
   - plannedStart < plannedEnd
   - startTime < endTime (when both set)
   - notifyBeforeStart < estimatedDuration

2. **Scheduling Logic**:
   - If isScheduled = true, plannedStart and plannedEnd required
   - plannedStart should be future time

3. **Active Maintenance**:
   - If isActive = true, startTime must be set
   - If isActive = false and startTime set, endTime should be set

4. **Compensation**:
   - If enabled = true, baseRewards must have at least one item
   - expirationDays must be > 0

5. **Duration Ranges**:
   - estimatedDuration: 600-86400 seconds (10 min - 24 hours)
   - notifyBeforeStart: 300-604800 seconds (5 min - 7 days)

6. **Type-Severity Matching**:
   - Emergency type should have Critical severity
   - Hotfix type should have Minor severity
   - Scheduled type usually Normal severity

7. **Affected Services**:
   - Must be valid service names
   - Empty array = all services affected

8. **Monitoring**:
   - healthCheckInterval: 30-600 seconds (reasonable range)

---

## Design Guidelines

### Scheduling Best Practices
- **Regular Schedule**: Consistent weekly maintenance (e.g., Tuesday 2 AM)
- **Off-Peak Hours**: Schedule during lowest player activity
- **Advance Notice**: Minimum 24 hours for scheduled maintenance
- **Duration Buffer**: Add 30% buffer to estimated duration
- **Communication**: Multiple notification channels

### Compensation Guidelines
- **Base Compensation**: 100 gems for 2-hour maintenance
- **Scaling**: +50 gems per additional hour
- **Emergency Bonus**: 2x compensation for unplanned maintenance
- **Maximum**: Cap at 500 gems (prevent abuse)
- **Additional Items**: Energy refills, summon scrolls for long downtimes

### Communication Strategy
- **Transparency**: Clear reasons for maintenance
- **Updates**: Regular status updates during extended maintenance
- **Apology**: Acknowledge inconvenience
- **Positive Framing**: "Thank you for patience" vs "Sorry for delay"

### Emergency Response
- **Speed**: Act fast (< 5 minutes to decision)
- **Safety**: Save player progress before disconnection
- **Communication**: Immediate notification
- **Follow-up**: Post-mortem and prevention plan

### Monitoring
- **Proactive**: Detect issues before players do
- **Automated**: Reduce manual monitoring burden
- **Thresholds**: Conservative (prevent false positives)
- **Escalation**: Clear escalation paths

---

## Common Maintenance Scenarios

### Weekly Scheduled Maintenance
```
Type: Scheduled
Duration: 2-4 hours
Frequency: Weekly (Tuesday)
Time: 2-6 AM UTC (off-peak)
Compensation: 100 gems + 2 energy refills
```

### Content Update Deployment
```
Type: Scheduled
Duration: 4-6 hours
Frequency: Monthly
Compensation: 200 gems + 10 summon scrolls
Includes: New characters, events, features
```

### Database Migration
```
Type: Scheduled
Duration: 6-8 hours
Frequency: Quarterly
Compensation: 300 gems + 5 energy + 10 scrolls
Critical: Data integrity priority
```

### Critical Bug Fix
```
Type: Emergency
Duration: 1-3 hours
Frequency: As needed
Compensation: 200-400 gems (based on duration)
Urgency: Immediate
```

### Server Crash Recovery
```
Type: Emergency
Duration: 2-6 hours
Frequency: Rare
Compensation: 400-500 gems + significant items
Priority: Data recovery and stability
```

### Exploit Mitigation
```
Type: Emergency
Duration: 2-4 hours
Frequency: Rare
Compensation: 300 gems + rollback compensation
Actions: Fix exploit, rollback affected data, ban abusers
```

---

## Integration with Other Systems

### KillSwitch.json Integration
```
Maintenance triggers:
- KillSwitch maintenance mode activated
- Maintenance.json isActive = true

Coordination:
- Both systems should sync
- KillSwitch = emergency control
- Maintenance.json = scheduled control
```

### FeatureFlags.json Integration
```
Partial maintenance:
- Disable specific features via FeatureFlags
- Set affectedServices in Maintenance
- Players see feature unavailable (not full maintenance)
```

### EventSchedule.json Integration
```
Event conflict prevention:
- Check event schedules before maintenance
- Avoid major event periods
- Extend event if maintenance overlaps
```

### Rewards.json Integration
```
Compensation items:
- Reference itemIds from Rewards.json
- Use standard reward structures
- Deliver via same systems (mailbox)
```

---

## Related Configuration Files

- **KillSwitch.json**: Emergency controls and feature gates
- **SystemSettings.json**: Server and client configuration
- **FeatureFlags.json**: Feature toggles for partial maintenance
- **EventSchedule.json**: Event timing coordination
- **Rewards.json**: Compensation item definitions
- **Manifest.json**: Configuration file versioning and updates

---

## Version History
- **1.0.0** (2025-11-23): Initial schema documentation
