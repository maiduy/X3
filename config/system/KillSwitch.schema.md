# KillSwitch.json Schema Documentation

## Overview
Defines emergency controls, maintenance mode, feature gates, and system health monitoring for rapid response to critical issues. Enables remote disabling of features, content, and entire systems without requiring client updates.

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

### `maintenance` (object)
- **Description**: Server maintenance mode configuration
- **Required**: Yes

### `emergencyMode` (object)
- **Description**: Emergency degraded mode settings
- **Required**: Yes

### `disabledFeatures` (array)
- **Type**: Array of Feature IDs
- **Description**: Features currently disabled via kill switch
- **Default**: `[]`

### `disabledCharacters` (array)
- **Type**: Array of Character IDs
- **Description**: Characters disabled due to bugs/exploits
- **Default**: `[]`

### `disabledItems` (array)
- **Type**: Array of Item IDs
- **Description**: Items disabled due to bugs/exploits
- **Default**: `[]`

### `disabledSkills` (array)
- **Type**: Array of Skill IDs
- **Description**: Skills disabled due to bugs/exploits
- **Default**: `[]`

### `disabledStages` (array)
- **Type**: Array of Stage IDs
- **Description**: Stages disabled due to issues
- **Default**: `[]`

### `disabledBanners` (array)
- **Type**: Array of Banner IDs
- **Description**: Gacha banners disabled
- **Default**: `[]`

### `disabledProducts` (array)
- **Type**: Array of Product IDs
- **Description**: IAP products disabled
- **Default**: `[]`

### `disabledEvents` (array)
- **Type**: Array of Event IDs
- **Description**: Events disabled
- **Default**: `[]`

### `disabledQuests` (array)
- **Type**: Array of Quest IDs
- **Description**: Quests disabled
- **Default**: `[]`

### `forceUpdateRequired` (object)
- **Description**: Force client update configuration
- **Required**: Yes

### `hotfixValues` (object)
- **Description**: Dynamic boolean flags for quick toggles
- **Required**: Yes

### `rateLimit` (object)
- **Description**: API rate limiting configuration
- **Required**: Yes

### `antiAbuse` (object)
- **Description**: Anti-cheat and abuse detection settings
- **Required**: Yes

### `serverHealth` (object)
- **Description**: Server health monitoring thresholds
- **Required**: Yes

### `featureGates` (object)
- **Description**: Granular feature control with metadata
- **Required**: Yes

### `contentBlacklist` (object)
- **Description**: Banned content and users
- **Required**: Yes

### `fallbackConfig` (object)
- **Description**: Fallback values when server unavailable
- **Required**: Yes

### `monitoring` (object)
- **Description**: Error reporting and tracking configuration
- **Required**: Yes

### `notifications` (object)
- **Description**: Player notification settings
- **Required**: Yes

---

## Maintenance Object

### `isActive` (boolean)
- **Description**: Whether maintenance mode is active
- **Default**: `false`
- **Usage**: Set to `true` to enable maintenance mode
- **Effect**: Blocks all player logins except whitelisted users

### `message` (string)
- **Type**: Display Text
- **Description**: Maintenance message shown to players
- **Example**: `"Server is under maintenance. Please try again later."`
- **Required**: Yes

### `messageKey` (string)
- **Type**: Localization Key
- **Description**: Localization key for maintenance message
- **Format**: `MAINTENANCE_MESSAGE_[TYPE]`
- **Example**: `"MAINTENANCE_MESSAGE_DEFAULT"`

### `estimatedEndTime` (string | null)
- **Type**: ISO 8601 DateTime or null
- **Description**: Expected maintenance end time
- **Example**: `"2025-12-01T14:00:00Z"`
- **Null**: No estimated end time
- **Usage**: Display countdown to players

### `allowedUsers` (array)
- **Type**: Array of User IDs
- **Description**: User IDs allowed during maintenance
- **Example**: `["admin_user_1", "tester_1"]`
- **Usage**: Dev team testing during maintenance

### `allowedIPs` (array)
- **Type**: Array of IP Addresses
- **Description**: IP addresses allowed during maintenance
- **Example**: `["192.168.1.100", "10.0.0.5"]`
- **Usage**: Office network access

### `allowTestAccounts` (boolean)
- **Description**: Allow test accounts during maintenance
- **Default**: `false`
- **Usage**: Enable QA testing

### `redirectUrl` (string | null)
- **Type**: URL or null
- **Description**: Redirect players to external page
- **Example**: `"https://game.com/maintenance-notice"`
- **Null**: No redirect
- **Usage**: Status page during maintenance

---

## Emergency Mode Object

### `enabled` (boolean)
- **Description**: Whether emergency mode is active
- **Default**: `false`
- **Usage**: Degraded mode for server issues
- **Effect**: Disables non-essential features

### `level` (string)
- **Values**: `"None"`, `"Low"`, `"Medium"`, `"High"`, `"Critical"`
- **Description**: Emergency severity level
- **Default**: `"None"`

**Level Descriptions**:
- **None**: Normal operation
- **Low**: Minor issues, non-critical features disabled
- **Medium**: Significant issues, some core features limited
- **High**: Major issues, minimal functionality only
- **Critical**: Severe issues, read-only mode or imminent shutdown

### `actions` (object)
Emergency response actions.

#### `disableIAP` (boolean)
- **Description**: Disable in-app purchases
- **Default**: `false`
- **Usage**: Prevent purchase issues during problems
- **Critical**: Protects revenue integrity

#### `disablePvP` (boolean)
- **Description**: Disable PvP/Arena modes
- **Default**: `false`
- **Usage**: Prevent exploits in competitive modes

#### `disableGacha` (boolean)
- **Description**: Disable gacha/summoning
- **Default**: `false`
- **Usage**: Prevent incorrect pulls during issues

#### `disableChat` (boolean)
- **Description**: Disable chat system
- **Default**: `false`
- **Usage**: Reduce server load or stop spam

#### `readOnlyMode` (boolean)
- **Description**: Allow viewing only, no gameplay
- **Default**: `false`
- **Usage**: Severe issues, data protection mode

### `message` (string)
- **Type**: Display Text
- **Description**: Emergency mode message to players
- **Example**: `"The game is experiencing technical difficulties. Some features may be temporarily unavailable."`

---

## Disabled Content Arrays

### Purpose
Quick disable of specific content without code changes.

### `disabledFeatures` (array)
- **Example**: `["FEATURE_PVP_ARENA", "FEATURE_GUILD_SYSTEM"]`
- **References**: Feature IDs from FeatureFlags.json
- **Effect**: Complete feature unavailability

### `disabledCharacters` (array)
- **Example**: `["CHAR_WARRIOR_THERON", "CHAR_MAGE_ARIA"]`
- **References**: Character IDs from Characters.json
- **Effect**:
  - Can't be selected for battles
  - Hidden in character selection
  - Existing teams using them show warning

### `disabledItems` (array)
- **Example**: `["ITEM_WEAPON_EXPLOIT_SWORD"]`
- **References**: Item IDs from Items.json
- **Effect**:
  - Can't be equipped
  - Can't be purchased
  - Can't drop from stages

### `disabledSkills` (array)
- **Example**: `["SKILL_INFINITE_DMG_BUG"]`
- **References**: Skill IDs from Skills.json
- **Effect**:
  - Skill greyed out in combat
  - Can't be used
  - AI won't use it

### `disabledStages` (array)
- **Example**: `["STAGE_1_1", "STAGE_BOSS_CHAPTER_1"]`
- **References**: Stage IDs from Levels.json
- **Effect**:
  - Can't be entered
  - Shows "Temporarily Unavailable"

### `disabledBanners` (array)
- **Example**: `["BANNER_RATE_BUG_EVENT"]`
- **References**: Banner IDs from Banners.json
- **Effect**:
  - Hidden from gacha screen
  - Can't pull
  - Prevents incorrect rates

### `disabledProducts` (array)
- **Example**: `["PRODUCT_BUGGED_PACK"]`
- **References**: Product IDs from ShopProducts.json
- **Effect**:
  - Hidden from shop
  - Can't be purchased
  - Prevents payment issues

### `disabledEvents` (array)
- **Example**: `["EVENT_DRAGON_RAID"]`
- **References**: Event IDs from EventSchedule.json
- **Effect**:
  - Event doesn't appear
  - Can't access event content

### `disabledQuests` (array)
- **Example**: `["QUEST_DAILY_PVP_001"]`
- **References**: Quest IDs from Quests.json
- **Effect**:
  - Quest hidden
  - No progress tracking

---

## Force Update Required Object

### `enabled` (boolean)
- **Description**: Whether force update is required
- **Default**: `false`
- **Usage**: Critical bugs requiring client patch

### `minVersion` (string)
- **Type**: Semantic Version
- **Description**: Minimum required client version
- **Format**: `MAJOR.MINOR.PATCH`
- **Example**: `"1.0.0"`
- **Effect**: Versions below this are blocked

### `message` (string)
- **Type**: Display Text
- **Description**: Update prompt message
- **Example**: `"A new version is available. Please update to continue playing."`

### `storeUrl` (object)
Store download links.

#### `ios` (string)
- **Type**: App Store URL
- **Description**: iOS app download link
- **Example**: `"https://apps.apple.com/app/id123456789"`

#### `android` (string)
- **Type**: Google Play URL
- **Description**: Android app download link
- **Example**: `"https://play.google.com/store/apps/details?id=com.game.example"`

---

## Hotfix Values Object

### Purpose
Quick boolean toggles without schema changes.

### `global_chat_enabled` (boolean)
- **Description**: Enable/disable global chat
- **Default**: `true`
- **Usage**: Quick spam control

### `friend_invites_enabled` (boolean)
- **Description**: Enable/disable friend system
- **Default**: `true`
- **Usage**: Prevent invite spam

### `leaderboard_enabled` (boolean)
- **Description**: Enable/disable leaderboards
- **Default**: `true`
- **Usage**: Fix ranking bugs

### `daily_login_enabled` (boolean)
- **Description**: Enable/disable daily login rewards
- **Default**: `true`
- **Usage**: Fix reward bugs

### `energy_refill_enabled` (boolean)
- **Description**: Enable/disable energy refills
- **Default**: `true`
- **Usage**: Prevent refill exploits

**Usage Pattern**:
```json
{
  "hotfixValues": {
    "pvp_matchmaking_enabled": true,
    "guild_wars_enabled": false,
    "raid_damage_multiplier_enabled": true
  }
}
```

---

## Rate Limit Object

### `api_requests_per_minute` (number)
- **Type**: Integer
- **Description**: Max API requests per player per minute
- **Default**: `60`
- **Usage**: Prevent API spam/DDoS

### `gacha_pulls_per_minute` (number)
- **Type**: Integer
- **Description**: Max gacha pulls per minute
- **Default**: `10`
- **Usage**: Prevent exploit scripts

### `shop_purchases_per_minute` (number)
- **Type**: Integer
- **Description**: Max shop purchases per minute
- **Default**: `5`
- **Usage**: Prevent purchase exploits

### `battle_starts_per_minute` (number)
- **Type**: Integer
- **Description**: Max battle starts per minute
- **Default**: `10`
- **Usage**: Prevent energy/reward exploits

**Rate Limit Response**:
```
Player exceeds limit:
- HTTP 429 Too Many Requests
- Temporary cooldown (1-5 minutes)
- Warning message
- Repeated violations = temporary ban
```

---

## Anti-Abuse Object

### `enableSpamDetection` (boolean)
- **Description**: Detect and block spam behavior
- **Default**: `true`
- **Usage**: Chat/friend spam prevention

### `enableDupeDetection` (boolean)
- **Description**: Detect item duplication exploits
- **Default**: `true`
- **Usage**: Economy protection

### `enableCheatDetection` (boolean)
- **Description**: Detect client-side cheating
- **Default**: `true`
- **Usage**: Speedhacks, memory editing detection

### `suspiciousActivityThreshold` (number)
- **Type**: Integer
- **Description**: Suspicious actions before flagging
- **Default**: `10`
- **Usage**: Automated detection sensitivity

### `autoBlockEnabled` (boolean)
- **Description**: Automatically block detected cheaters
- **Default**: `false`
- **Usage**: Manual review vs auto-ban
- **Caution**: False positives possible

**Detection Examples**:
- Impossible currency amounts
- Abnormal battle clear times
- Unrealistic damage values
- Item duplication patterns
- Rapid repeated actions

---

## Server Health Object

### `cpuThreshold` (number)
- **Type**: Integer (0-100%)
- **Description**: CPU usage warning threshold
- **Default**: `80`
- **Effect**: Alert when CPU > 80%

### `memoryThreshold` (number)
- **Type**: Integer (0-100%)
- **Description**: Memory usage warning threshold
- **Default**: `85`
- **Effect**: Alert when memory > 85%

### `autoScaleEnabled` (boolean)
- **Description**: Automatically scale server capacity
- **Default**: `true`
- **Usage**: Cloud auto-scaling trigger

### `degradedModeThreshold` (number)
- **Type**: Integer (0-100%)
- **Description**: Enter degraded mode at this usage
- **Default**: `90`
- **Effect**: Activate emergency mode when > 90%

**Degraded Mode Actions**:
1. CPU/Memory exceeds threshold
2. Automatically enable emergency mode (Medium level)
3. Disable non-essential features
4. Notify operations team
5. Scale up resources if autoScale enabled

---

## Feature Gates Object

### Purpose
Granular feature control with tracking.

### Feature Gate Structure
```json
{
  "FEATURE_PVP_ARENA": {
    "enabled": true,
    "reason": null,
    "disabledSince": null
  }
}
```

### `FEATURE_PVP_ARENA` (object)

#### `enabled` (boolean)
- **Description**: Whether PvP arena is available
- **Default**: `true`

#### `reason` (string | null)
- **Description**: Why feature is disabled
- **Example**: `"Critical bug detected in matchmaking"`
- **Null**: No specific reason (enabled)

#### `disabledSince` (string | null)
- **Type**: ISO 8601 DateTime or null
- **Description**: When feature was disabled
- **Example**: `"2025-11-20T00:00:00Z"`
- **Null**: Not disabled

### Common Feature Gates

#### `FEATURE_GUILD_SYSTEM`
- **Usage**: Guild features
- **Example Reason**: `"Under development"`

#### `FEATURE_CHAT_SYSTEM`
- **Usage**: In-game chat
- **Example Reason**: `"Spam attack mitigation"`

#### `FEATURE_GACHA`
- **Usage**: Gacha/summoning system
- **Example Reason**: `"Rate calculation bug fix"`

#### `FEATURE_IAP`
- **Usage**: In-app purchases
- **Example Reason**: `"Payment provider issues"`

---

## Content Blacklist Object

### `bannedWords` (array)
- **Type**: Array of strings
- **Description**: Filtered words in chat/names
- **Example**: `["badword1", "badword2"]`
- **Usage**: Content moderation

### `bannedUserIds` (array)
- **Type**: Array of User IDs
- **Description**: Banned player accounts
- **Example**: `["user_cheater_123"]`
- **Effect**: Login blocked

### `bannedDeviceIds` (array)
- **Type**: Array of Device IDs
- **Description**: Hardware-banned devices
- **Example**: `["device_abc123"]`
- **Effect**: Device can't access game

### `restrictedRegions` (array)
- **Type**: Array of Region Codes
- **Description**: Regions where game is unavailable
- **Example**: `["XX"]`
- **Usage**: Geo-blocking, legal compliance

---

## Fallback Config Object

### Purpose
Default values when server/config unavailable.

### `useLocalCache` (boolean)
- **Description**: Use cached config if server unreachable
- **Default**: `true`
- **Usage**: Offline playability

### `maxCacheAge` (number)
- **Type**: Integer (seconds)
- **Description**: Maximum cache age before refresh required
- **Default**: `86400` (24 hours)

### `defaultDropRates` (object)
Default gacha rates when config unavailable.

#### `SSR` (number)
- **Type**: Float (0.0-1.0)
- **Description**: Default SSR drop rate
- **Default**: `0.02` (2%)

#### `SR` (number)
- **Type**: Float (0.0-1.0)
- **Description**: Default SR drop rate
- **Default**: `0.18` (18%)

#### `R` (number)
- **Type**: Float (0.0-1.0)
- **Description**: Default R drop rate
- **Default**: `0.80` (80%)

### `defaultEnergyRegenRate` (number)
- **Type**: Integer
- **Description**: Energy points per interval
- **Default**: `1`

### `defaultEnergyRegenInterval` (number)
- **Type**: Integer (seconds)
- **Description**: Seconds between energy regen
- **Default**: `300` (5 minutes)

**Fallback Usage**:
```
Scenario: Server unreachable
1. Client detects connection failure
2. Checks local cache age
3. If cache < maxCacheAge:
   - Use cached config
4. If cache expired:
   - Use fallbackConfig values
   - Limit functionality to PvE only
   - Disable IAP/competitive features
```

---

## Monitoring Object

### `enableErrorReporting` (boolean)
- **Description**: Send error reports to analytics
- **Default**: `true`
- **Usage**: Bug tracking

### `enablePerformanceTracking` (boolean)
- **Description**: Track performance metrics
- **Default**: `true`
- **Usage**: Optimization insights

### `enableUserBehaviorTracking` (boolean)
- **Description**: Track player behavior analytics
- **Default**: `true`
- **Usage**: Engagement analysis

### `samplingRate` (number)
- **Type**: Float (0.0-1.0)
- **Description**: Percentage of events to track
- **Default**: `0.1` (10%)
- **Usage**: Reduce analytics volume

---

## Notifications Object

### `sendMaintenanceNotification` (boolean)
- **Description**: Push notification for maintenance
- **Default**: `true`
- **Usage**: Advance warning to players

### `sendEmergencyNotification` (boolean)
- **Description**: Push notification for emergencies
- **Default**: `true`
- **Usage**: Critical issue alerts

### `notificationChannels` (array)
- **Type**: Array of Channel Names
- **Values**: `["Push", "InGame", "Email"]`
- **Description**: Notification delivery methods
- **Usage**: Multi-channel communication

---

## Emergency Response Workflows

### Workflow 1: Critical Bug Discovered

#### Step 1: Identify Issue
```
Bug: Character skill crashes game
Affected: CHAR_WARRIOR_THERON
```

#### Step 2: Immediate Kill Switch
```json
{
  "disabledCharacters": ["CHAR_WARRIOR_THERON"]
}
```

#### Step 3: Notify Players
```
In-game message: "Theron temporarily unavailable due to technical issues"
```

#### Step 4: Deploy Fix
```
1. Fix skill in code
2. Test thoroughly
3. Deploy patch
4. Remove from disabledCharacters
```

### Workflow 2: Gacha Rate Bug

#### Step 1: Detect Issue
```
Issue: SSR rate showing 100% instead of 2%
```

#### Step 2: Emergency Disable
```json
{
  "disabledBanners": ["BANNER_STANDARD"],
  "emergencyMode": {
    "enabled": true,
    "level": "High",
    "actions": {
      "disableGacha": true
    }
  }
}
```

#### Step 3: Investigate Impact
```
- How many pulls affected?
- Identify affected players
- Calculate compensation
```

#### Step 4: Resolution
```json
{
  "disabledBanners": [],
  "emergencyMode": {
    "enabled": false,
    "level": "None"
  }
}
```

### Workflow 3: Server Overload

#### Step 1: Detect High Load
```
Server CPU: 92%
Memory: 88%
degradedModeThreshold: 90% exceeded
```

#### Step 2: Auto-Degraded Mode
```json
{
  "emergencyMode": {
    "enabled": true,
    "level": "Medium",
    "actions": {
      "disableChat": true,
      "disablePvP": true
    }
  }
}
```

#### Step 3: Scale Resources
```
- autoScaleEnabled: true
- Spin up additional servers
- Distribute load
```

#### Step 4: Recovery
```json
{
  "emergencyMode": {
    "enabled": false,
    "level": "None"
  }
}
```

### Workflow 4: Planned Maintenance

#### Step 1: Schedule Maintenance
```
Planned: 2025-12-01 02:00-06:00 UTC
Reason: Database migration
```

#### Step 2: Notify Players (24h advance)
```
Push notification: "Server maintenance Dec 1, 2-6 AM UTC"
In-game banner: Countdown to maintenance
```

#### Step 3: Activate Maintenance Mode
```json
{
  "maintenance": {
    "isActive": true,
    "message": "Server maintenance in progress. Estimated completion: 06:00 UTC",
    "estimatedEndTime": "2025-12-01T06:00:00Z",
    "allowedUsers": ["admin1", "admin2"],
    "allowTestAccounts": true
  }
}
```

#### Step 4: Complete Maintenance
```json
{
  "maintenance": {
    "isActive": false
  }
}
```

---

## Common Scenarios

### Scenario 1: Exploit Discovery
```
Issue: Item duplication exploit found
Impact: Economy broken

Response:
1. Add exploit item to disabledItems
2. Enable anti-abuse autoBlock
3. Investigate affected accounts
4. Roll back exploited items
5. Ban repeat offenders
6. Deploy patch
7. Re-enable item
```

### Scenario 2: Payment Processing Failure
```
Issue: IAP purchases not completing
Impact: Revenue loss, angry players

Response:
1. Emergency disable IAP
2. Investigate payment provider
3. Queue failed transactions
4. Fix integration
5. Retry queued purchases
6. Re-enable IAP
7. Compensate affected players
```

### Scenario 3: Chat Spam Attack
```
Issue: Spam bots flooding global chat
Impact: Unplayable chat, server load

Response:
1. Disable chat via hotfixValues
2. Add spam keywords to bannedWords
3. Implement stricter rateLimit
4. Ban spam accounts
5. Re-enable chat with limits
```

### Scenario 4: Event Bug
```
Issue: Event rewards giving 100x intended amount
Impact: Economy inflation

Response:
1. Add event to disabledEvents immediately
2. Identify affected players
3. Calculate excess rewards
4. Remove excess items/currency
5. Fix event configuration
6. Re-enable event
7. Announce fix
```

---

## Data Validation Rules

1. **Maintenance Consistency**:
   - If isActive = true, message must be set
   - estimatedEndTime should be in future

2. **Emergency Mode Logic**:
   - If enabled = true, level ≠ "None"
   - Actions should match level severity

3. **Array Uniqueness**:
   - No duplicate IDs in disabled arrays
   - IDs should reference valid content

4. **Force Update**:
   - If enabled = true, minVersion must be specified
   - storeUrl required for both platforms

5. **Rate Limits**:
   - All values must be > 0
   - Reasonable ranges (not too restrictive)

6. **Feature Gates**:
   - If enabled = false, reason should be set
   - disabledSince should be set when disabled

7. **Fallback Rates**:
   - defaultDropRates should sum to 1.0
   - All rates 0.0-1.0

8. **Monitoring**:
   - samplingRate must be 0.0-1.0

9. **Server Health**:
   - Thresholds should be 0-100
   - degradedModeThreshold ≥ cpuThreshold

---

## Design Guidelines

### Response Time
- **Critical**: Disable within 1 minute of detection
- **High**: Disable within 5 minutes
- **Medium**: Disable within 30 minutes
- **Low**: Schedule fix for next deployment

### Communication
- **Always Notify**: Inform players of disruptions
- **Be Transparent**: Explain issues when appropriate
- **Set Expectations**: Provide ETAs when possible
- **Apologize**: Acknowledge inconvenience

### Testing
- **Test Kill Switches**: Regularly test disable mechanisms
- **Staging First**: Test emergency procedures in staging
- **Rollback Plan**: Always have undo process ready

### Monitoring
- **Alert Thresholds**: Set up automated alerts
- **Dashboard**: Real-time monitoring dashboard
- **Logs**: Comprehensive logging of all actions

### Compensation
- **Fair**: Compensate affected players appropriately
- **Timely**: Distribute compensation quickly
- **Transparent**: Explain compensation criteria

---

## Security Considerations

### Access Control
- Only authorized personnel can modify KillSwitch.json
- Audit log of all changes
- Multi-factor authentication required

### Fail-Safe Defaults
- Default to disabled for risky features
- Default to enabled for critical features (login, etc.)
- Fallback config should allow basic gameplay

### Abuse Prevention
- Rate limiting prevents brute force
- Device bans for hardware-level bans
- IP restrictions for geographic compliance

### Data Integrity
- Validate all configuration before deployment
- Automated testing of kill switch activation
- Backup configs before changes

---

## Related Configuration Files

- **FeatureFlags.json**: Feature rollout and A/B testing
- **GlobalModifiers.json**: Temporary gameplay modifiers
- **SystemSettings.json**: Core system configuration
- **EventSchedule.json**: Event scheduling and content
- **Characters.json**: Character definitions (disabledCharacters)
- **Items.json**: Item definitions (disabledItems)
- **Skills.json**: Skill definitions (disabledSkills)
- **Levels.json**: Stage definitions (disabledStages)

---

## Version History
- **1.0.0** (2025-11-23): Initial schema documentation
