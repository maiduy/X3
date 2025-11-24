# SystemSettings.json Schema Documentation

## Overview
This document provides comprehensive descriptions for all indices (fields) in the `SystemSettings.json` configuration file. This file defines core system configurations including server settings, client parameters, gameplay limits, economy caps, social features, performance settings, security, analytics, localization, and debug options.

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

---

## Server Object

### `server` (object)
Backend server configuration and connection parameters.

#### `environment` (string)
- **Type**: Environment Enum
- **Description**: Current server environment
- **Values**: `"Development"`, `"Staging"`, `"Production"`
- **Example**: `"Production"`
- **Usage**: Determines logging, error handling, debugging

#### `apiVersion` (string)
- **Type**: API Version String
- **Description**: API endpoint version in use
- **Format**: `v{number}`
- **Example**: `"v1"`
- **Usage**: API routing, backward compatibility

#### `baseUrl` (string)
- **Type**: URL
- **Description**: Primary API server endpoint
- **Example**: `"https://api.game.example.com"`
- **Usage**: All API requests prefix

#### `cdnUrl` (string)
- **Type**: URL
- **Description**: Content Delivery Network URL
- **Example**: `"https://cdn.game.example.com"`
- **Usage**: Asset downloads, images, bundles

#### `timeout` (number)
- **Type**: Integer (milliseconds)
- **Description**: Request timeout duration
- **Default**: `30000` (30 seconds)
- **Usage**: Cancel requests exceeding this time

#### `retryAttempts` (number)
- **Type**: Integer
- **Description**: Number of retry attempts for failed requests
- **Default**: `3`
- **Range**: 1-5
- **Usage**: Network resilience

#### `retryDelay` (number)
- **Type**: Integer (milliseconds)
- **Description**: Delay between retry attempts
- **Default**: `1000` (1 second)
- **Usage**: Exponential backoff calculation

---

## Client Object

### `client` (object)
Client application version control and maintenance.

#### `minSupportedVersion` (string)
- **Type**: Semantic Version
- **Description**: Minimum client version that can connect
- **Format**: `MAJOR.MINOR.PATCH`
- **Example**: `"1.0.0"`
- **Usage**: Older clients are blocked

#### `currentVersion` (string)
- **Type**: Semantic Version
- **Description**: Latest available client version
- **Example**: `"1.0.0"`
- **Usage**: Version comparison, update prompts

#### `forceUpdateVersion` (string)
- **Type**: Semantic Version
- **Description**: Versions at or below this must update
- **Example**: `"0.9.0"`
- **Usage**: Force critical updates

#### `updateCheckInterval` (number)
- **Type**: Integer (seconds)
- **Description**: Frequency of version checks
- **Default**: `3600` (1 hour)
- **Usage**: Periodic update detection

#### `maintenanceMode` (object)
Planned maintenance configuration.

##### `enabled` (boolean)
- **Type**: Boolean
- **Description**: Whether maintenance mode is active
- **Default**: `false`
- **Usage**: Block all logins during maintenance

##### `message` (string)
- **Type**: Display Text
- **Description**: Message shown to blocked users
- **Example**: `"Server maintenance in progress. Please try again later."`

##### `estimatedDuration` (number)
- **Type**: Integer (seconds)
- **Description**: Expected maintenance duration
- **Example**: `7200` (2 hours)
- **Usage**: Display countdown to users

---

## Gameplay Object

### `gameplay` (object)
Core gameplay limits and configurations.

#### `maxTeamSize` (number)
- **Type**: Integer
- **Description**: Maximum characters in a team
- **Default**: `5`
- **Range**: 3-5

#### `maxCharacterLevel` (number)
- **Type**: Integer
- **Description**: Maximum level for characters
- **Default**: `80`
- **Usage**: Level cap enforcement

#### `maxItemLevel` (number)
- **Type**: Integer
- **Description**: Maximum level for equipment
- **Default**: `90`
- **Usage**: Equipment enhancement cap

#### `energyConfig` (object)
Energy/stamina system configuration.

##### `maxEnergy` (number)
- **Type**: Integer
- **Description**: Absolute maximum energy capacity
- **Default**: `200`
- **Usage**: Hard cap including refills

##### `naturalCap` (number)
- **Type**: Integer
- **Description**: Maximum energy from natural regeneration
- **Default**: `100`
- **Usage**: Natural regen stops here

##### `regenRate` (number)
- **Type**: Integer
- **Description**: Energy restored per interval
- **Default**: `1`
- **Usage**: Regen calculation

##### `regenInterval` (number)
- **Type**: Integer (seconds)
- **Description**: Time between regen ticks
- **Default**: `300` (5 minutes)
- **Calculation**: `1 energy per 5 minutes = 12 energy/hour = 288 energy/day`

#### `battleConfig` (object)
Combat system settings.

##### `maxTurnLimit` (number)
- **Type**: Integer
- **Description**: Default maximum turns per battle
- **Default**: `50`
- **Links to**: BattleRules.json

##### `turnTimeout` (number)
- **Type**: Integer (seconds)
- **Description**: Time limit per player turn
- **Default**: `30`

##### `autoPlayUnlockLevel` (number)
- **Type**: Integer
- **Description**: Player level to unlock auto-battle
- **Default**: `5`
- **Usage**: Feature gate

##### `speedUpUnlockLevel` (number)
- **Type**: Integer
- **Description**: Player level to unlock battle speed-up
- **Default**: `10`
- **Usage**: Feature gate

#### `inventoryConfig` (object)
Inventory capacity limits.

##### `maxCharacters` (number)
- **Type**: Integer
- **Description**: Maximum character inventory slots
- **Default**: `200`
- **Usage**: Roster size limit

##### `maxItems` (number)
- **Type**: Integer
- **Description**: Maximum equipment inventory slots
- **Default**: `500`

##### `maxMaterials` (number)
- **Type**: Integer
- **Description**: Maximum material stack size
- **Default**: `9999`

##### `autoSellThreshold` (number | null)
- **Type**: Integer or Null
- **Description**: Auto-sell items below this rarity
- **Default**: `null` (disabled)
- **Usage**: Inventory management

---

## Economy Object

### `economy` (object)
Currency and economic system settings.

#### `goldCap` (number)
- **Type**: Integer
- **Description**: Maximum soft currency (gold)
- **Default**: `999999999`

#### `gemCap` (number)
- **Type**: Integer
- **Description**: Maximum premium currency (gems)
- **Default**: `999999999`

#### `dailyGoldLimit` (number | null)
- **Type**: Integer or Null
- **Description**: Daily gold earning cap
- **Default**: `null` (unlimited)

#### `dailyGemSpendLimit` (number | null)
- **Type**: Integer or Null
- **Description**: Daily gem spending cap
- **Default**: `null` (unlimited)

#### `shopRefreshTimes` (object)
Shop reset schedule.

##### `dailyShop` (string)
- **Type**: Time String
- **Description**: Daily shop reset time
- **Format**: `"HH:MM:SS"`
- **Example**: `"00:00:00"` (midnight UTC)

##### `weeklyShop` (string)
- **Type**: Day and Time String
- **Description**: Weekly shop reset schedule
- **Format**: `"DayOfWeek_HH:MM:SS"`
- **Example**: `"Monday_00:00:00"`

#### `gachaConfig` (object)
Gacha/summoning system settings.

##### `singlePullCost` (number)
- **Type**: Integer
- **Description**: Gem cost for single summon
- **Default**: `160`

##### `multiPullCost` (number)
- **Type**: Integer
- **Description**: Gem cost for 10-pull
- **Default**: `1600`

##### `multiPullCount` (number)
- **Type**: Integer
- **Description**: Number of summons in multi-pull
- **Default**: `10`

##### `multiPullDiscount` (number)
- **Type**: Integer
- **Description**: Discount for multi-pull (gems saved)
- **Default**: `0` (no discount, but often has guarantee)

##### `historyRetention` (number)
- **Type**: Integer
- **Description**: Number of pull history records to keep
- **Default**: `100`

---

## Social Object

### `social` (object)
Social features and limitations.

#### `maxFriends` (number)
- **Type**: Integer
- **Description**: Maximum friend list size
- **Default**: `100`

#### `maxFriendRequests` (number)
- **Type**: Integer
- **Description**: Maximum pending friend requests
- **Default**: `50`

#### `friendStaminaGift` (number)
- **Type**: Integer
- **Description**: Energy sent per friend gift
- **Default**: `10`

#### `maxDailyFriendGifts` (number)
- **Type**: Integer
- **Description**: Maximum gifts sent/received per day
- **Default**: `50`

#### `chatMessageMaxLength` (number)
- **Type**: Integer (characters)
- **Description**: Maximum chat message length
- **Default**: `200`

#### `chatHistoryLimit` (number)
- **Type**: Integer (messages)
- **Description**: Chat messages stored locally
- **Default**: `100`

#### `reportCooldown` (number)
- **Type**: Integer (seconds)
- **Description**: Cooldown between player reports
- **Default**: `300` (5 minutes)
- **Usage**: Prevent report spam

---

## PvP Object

### `pvp` (object)
PvP system configuration.

#### `dailyFreeMatches` (number)
- **Type**: Integer
- **Description**: Free PvP matches per day
- **Default**: `5`

#### `energyCostPerMatch` (number)
- **Type**: Integer
- **Description**: Energy cost for PvP match
- **Default**: `0` (free)
- **Note**: Can be set to positive for energy cost

#### `matchmakingTimeout` (number)
- **Type**: Integer (seconds)
- **Description**: Maximum matchmaking search time
- **Default**: `60`

#### `seasonDuration` (number)
- **Type**: Integer (days)
- **Description**: Length of ranked season
- **Default**: `30`

#### `rankDecayEnabled` (boolean)
- **Type**: Boolean
- **Description**: Whether inactive players lose rank
- **Default**: `true`

#### `rankDecayDays` (number)
- **Type**: Integer (days)
- **Description**: Days of inactivity before decay
- **Default**: `7`

#### `rankDecayAmount` (number)
- **Type**: Integer (ELO)
- **Description**: ELO lost per decay period
- **Default**: `50`

---

## Performance Object

### `performance` (object)
Graphics and performance settings.

#### `targetFrameRate` (number)
- **Type**: Integer (FPS)
- **Description**: Target frames per second
- **Default**: `60`
- **Options**: `30`, `60`, `120`

#### `graphicsQuality` (object)
Quality presets for different tiers.

##### `low` (object)
Low-end device settings.

###### `shadowQuality` (number)
- **Type**: Integer (0-2)
- **Description**: Shadow rendering quality
- **Values**: `0` = off, `1` = low, `2` = high
- **Default**: `0`

###### `textureQuality` (number)
- **Type**: Integer (0-2)
- **Description**: Texture resolution level
- **Default**: `0`

###### `particleCount` (number)
- **Type**: Integer
- **Description**: Max simultaneous particles
- **Default**: `50`

##### `medium` (object)
Mid-range device settings.

###### `shadowQuality` (number)
- **Default**: `1`

###### `textureQuality` (number)
- **Default**: `1`

###### `particleCount` (number)
- **Default**: `100`

##### `high` (object)
High-end device settings.

###### `shadowQuality` (number)
- **Default**: `2`

###### `textureQuality` (number)
- **Default**: `2`

###### `particleCount` (number)
- **Default**: `200`

#### `assetBundleCompression` (boolean)
- **Type**: Boolean
- **Description**: Enable asset bundle compression
- **Default**: `true`
- **Trade-off**: Smaller downloads, longer load times

#### `assetBundleCaching` (boolean)
- **Type**: Boolean
- **Description**: Cache downloaded assets locally
- **Default**: `true`

#### `maxConcurrentDownloads` (number)
- **Type**: Integer
- **Description**: Maximum simultaneous asset downloads
- **Default**: `3`

---

## Notifications Object

### `notifications` (object)
Push and in-game notification settings.

#### `pushNotifications` (object)
OS-level push notification toggles.

##### `enabled` (boolean)
- **Type**: Boolean
- **Description**: Master toggle for push notifications
- **Default**: `true`

##### `energyFull` (boolean)
- **Type**: Boolean
- **Description**: Notify when energy is full
- **Default**: `true`

##### `eventStart` (boolean)
- **Type**: Boolean
- **Description**: Notify when events start
- **Default**: `true`

##### `guildNotifications` (boolean)
- **Type**: Boolean
- **Description**: Notify for guild activities
- **Default**: `true`

##### `friendRequests` (boolean)
- **Type**: Boolean
- **Description**: Notify for friend requests
- **Default**: `true`

#### `inGameNotifications` (object)
In-app notification banners.

##### `maintenance` (boolean)
- **Default**: `true`

##### `events` (boolean)
- **Default**: `true`

##### `updates` (boolean)
- **Default**: `true`

##### `achievements` (boolean)
- **Default**: `true`

---

## Security Object

### `security` (object)
Security and anti-cheat systems.

#### `antiCheat` (object)
Anti-cheat configuration.

##### `enabled` (boolean)
- **Type**: Boolean
- **Description**: Master toggle for anti-cheat
- **Default**: `true`

##### `clientValidation` (boolean)
- **Type**: Boolean
- **Description**: Validate client-side data
- **Default**: `true`

##### `serverValidation` (boolean)
- **Type**: Boolean
- **Description**: Server-side data validation
- **Default**: `true`

##### `checksumValidation` (boolean)
- **Type**: Boolean
- **Description**: Validate file integrity
- **Default**: `true`

#### `rateLimit` (object)
API rate limiting.

##### `enabled` (boolean)
- **Default**: `true`

##### `maxRequestsPerMinute` (number)
- **Type**: Integer
- **Description**: Max API calls per minute
- **Default**: `60`

##### `maxRequestsPerHour` (number)
- **Type**: Integer
- **Description**: Max API calls per hour
- **Default**: `1000`

#### `sessionTimeout` (number)
- **Type**: Integer (seconds)
- **Description**: Session expiration time
- **Default**: `86400` (24 hours)

#### `tokenRefreshInterval` (number)
- **Type**: Integer (seconds)
- **Description**: Auth token refresh frequency
- **Default**: `3600` (1 hour)

---

## Analytics Object

### `analytics` (object)
Analytics and telemetry configuration.

#### `enabled` (boolean)
- **Type**: Boolean
- **Description**: Master toggle for analytics
- **Default**: `true`

#### `trackingLevel` (string)
- **Type**: Tracking Level Enum
- **Description**: Detail level of tracking
- **Values**: `"Minimal"`, `"Standard"`, `"Detailed"`
- **Default**: `"Detailed"`

#### `batchSize` (number)
- **Type**: Integer
- **Description**: Events batched before upload
- **Default**: `50`

#### `uploadInterval` (number)
- **Type**: Integer (seconds)
- **Description**: Frequency of analytics uploads
- **Default**: `60`

#### `offlineQueueSize` (number)
- **Type**: Integer
- **Description**: Max events queued when offline
- **Default**: `1000`

#### `anonymousTracking` (boolean)
- **Type**: Boolean
- **Description**: Use anonymous IDs instead of user IDs
- **Default**: `false`

---

## Logging Object

### `logging` (object)
Application logging configuration.

#### `logLevel` (string)
- **Type**: Log Level Enum
- **Description**: Minimum log severity
- **Values**: `"Debug"`, `"Info"`, `"Warning"`, `"Error"`
- **Default**: `"Info"`

#### `enableRemoteLogging` (boolean)
- **Type**: Boolean
- **Description**: Send logs to remote server
- **Default**: `true`

#### `maxLocalLogs` (number)
- **Type**: Integer
- **Description**: Maximum local log entries
- **Default**: `100`

#### `sensitiveDataMasking` (boolean)
- **Type**: Boolean
- **Description**: Mask passwords, tokens in logs
- **Default**: `true`

#### `crashReporting` (object)
Crash report configuration.

##### `enabled` (boolean)
- **Default**: `true`

##### `autoUpload` (boolean)
- **Type**: Boolean
- **Description**: Automatically upload crash reports
- **Default**: `true`

##### `includeScreenshots` (boolean)
- **Type**: Boolean
- **Description**: Attach screenshots to crash reports
- **Default**: `false`

---

## Localization Object

### `localization` (object)
Multi-language support configuration.

#### `defaultLanguage` (string)
- **Type**: Language Code
- **Description**: Fallback language
- **Format**: ISO 639-1
- **Example**: `"en"`

#### `supportedLanguages` (array)
- **Type**: Array of Language Codes
- **Description**: Available languages
- **Example**: `["en", "zh", "ko"]`

#### `fallbackLanguage` (string)
- **Type**: Language Code
- **Description**: Used if key missing in selected language
- **Example**: `"en"`

#### `autoDetectLanguage` (boolean)
- **Type**: Boolean
- **Description**: Detect device language
- **Default**: `true`

#### `languagePackVersion` (string)
- **Type**: Semantic Version
- **Description**: Localization data version
- **Example**: `"1.0.0"`

#### `configPath` (string)
- **Type**: File Path
- **Description**: Main localization config path
- **Example**: `"localization/Localization.json"`

#### `languageFilesPath` (string)
- **Type**: File Path Template
- **Description**: Language file path pattern
- **Format**: Use `{lang}` placeholder
- **Example**: `"localization/languages/{lang}.json"`

#### `keyMappingPath` (string)
- **Type**: File Path
- **Description**: Localization key reference file
- **Example**: `"localization/LocalizationKeys.json"`

#### `cacheEnabled` (boolean)
- **Type**: Boolean
- **Description**: Cache loaded translations
- **Default**: `true`

#### `cacheDuration` (number)
- **Type**: Integer (seconds)
- **Description**: Translation cache TTL
- **Default**: `86400` (24 hours)

#### `hotReloadEnabled` (boolean)
- **Type**: Boolean
- **Description**: Reload translations without restart
- **Default**: `true`
- **Usage**: Development feature

#### `compressionEnabled` (boolean)
- **Type**: Boolean
- **Description**: Compress language files
- **Default**: `true`

#### `missingKeyBehavior` (string)
- **Type**: Behavior Enum
- **Description**: Action when key not found
- **Values**:
  - `"UseKey"` - Display the key itself
  - `"UseFallback"` - Use fallback language
  - `"ShowError"` - Display error message
- **Default**: `"UseKey"`

#### `logMissingKeys` (boolean)
- **Type**: Boolean
- **Description**: Log missing localization keys
- **Default**: `true`

#### `formatSupport` (object)
Formatting capabilities.

##### `date` (boolean)
- **Description**: Support date formatting
- **Default**: `true`

##### `time` (boolean)
- **Description**: Support time formatting
- **Default**: `true`

##### `number` (boolean)
- **Description**: Support number formatting
- **Default**: `true`

##### `currency` (boolean)
- **Description**: Support currency formatting
- **Default**: `true`

##### `pluralization` (boolean)
- **Description**: Support plural forms
- **Default**: `true`

##### `parameters` (boolean)
- **Description**: Support string interpolation
- **Default**: `true`

#### `validation` (object)
Localization validation rules.

##### `requireAllKeys` (boolean)
- **Type**: Boolean
- **Description**: Enforce all languages have all keys
- **Default**: `false`

##### `warnMissingKeys` (boolean)
- **Type**: Boolean
- **Description**: Warn about missing keys
- **Default**: `true`

##### `allowHTMLTags` (boolean)
- **Type**: Boolean
- **Description**: Allow HTML in translated text
- **Default**: `false`

##### `maxTextLength` (number)
- **Type**: Integer (characters)
- **Description**: Maximum translation length
- **Default**: `1000`

---

## Regions Object

### `regions` (object)
Server region configuration.

#### `default` (string)
- **Type**: Region ID
- **Description**: Default selected region
- **Example**: `"Global"`

#### `available` (array)
- **Type**: Array of Region Objects
- **Description**: Available server regions

##### Region Object:

###### `id` (string)
- **Type**: Region Identifier
- **Example**: `"Asia"`, `"EU"`, `"NA"`

###### `name` (string)
- **Type**: Display Name
- **Example**: `"Asia"`, `"Europe"`, `"North America"`

###### `serverUrl` (string)
- **Type**: URL
- **Description**: Region-specific API endpoint
- **Example**: `"https://api-asia.game.example.com"`

---

## Debug Object

### `debug` (object)
Developer debugging tools.

**WARNING**: All should be `false` in production.

#### `enableDebugMenu` (boolean)
- **Type**: Boolean
- **Description**: Show developer debug menu
- **Default**: `false`

#### `enableCheatCodes` (boolean)
- **Type**: Boolean
- **Description**: Allow cheat code input
- **Default**: `false`

#### `showFPS` (boolean)
- **Type**: Boolean
- **Description**: Display FPS counter
- **Default**: `false`

#### `showMemoryUsage` (boolean)
- **Type**: Boolean
- **Description**: Display memory usage stats
- **Default**: `false`

#### `enableGodMode` (boolean)
- **Type**: Boolean
- **Description**: Invincibility in battles
- **Default**: `false`

#### `skipTutorial` (boolean)
- **Type**: Boolean
- **Description**: Bypass tutorial on new accounts
- **Default**: `false`

---

## Data Validation Rules

1. **Version Comparison**: forceUpdateVersion ≤ minSupportedVersion ≤ currentVersion
2. **Positive Values**: All timeouts, limits, caps must be > 0
3. **URL Format**: All URLs must be valid HTTPS endpoints
4. **Language Codes**: Must be valid ISO 639-1 codes
5. **Quality Levels**: shadowQuality and textureQuality must be 0-2
6. **Rate Limits**: maxRequestsPerMinute * 60 should not exceed maxRequestsPerHour significantly
7. **Debug Flags**: All debug flags must be false in production environment
8. **Regen Math**: energyConfig.regenRate and regenInterval should provide reasonable daily energy

---

## Common Use Cases

### Energy Regeneration Calculation
```
Regen Rate: 1 energy per 300 seconds (5 minutes)
Hourly: 60 / 5 = 12 energy/hour
Daily: 12 * 24 = 288 energy/day
Max Natural Cap: 100 energy
Time to Full: 100 * 5 = 500 minutes = 8.3 hours
```

### Version Update Check
```
Client Version: 0.9.0
forceUpdateVersion: 0.9.0
Action: Force update required (0.9.0 ≤ 0.9.0)

Client Version: 1.0.0
minSupportedVersion: 1.0.0
Action: Allowed (1.0.0 ≥ 1.0.0)
```

### Rate Limiting
```
Max Requests/Minute: 60
Max Requests/Hour: 1000
Sustained Rate: 60 * 60 = 3600 requests/hour (exceeds limit)
Burst Allowed: Yes (60/min for short periods)
```

---

## Related Configuration Files

- **FeatureFlags.json**: Feature toggles and A/B tests
- **Maintenance.json**: Extended maintenance configuration
- **KillSwitch.json**: Emergency feature disabling
- **Analytics.json**: Detailed analytics events
- **Localization.json**: Localization system details
- **All Game Configs**: Reference these system limits

---

## Version History

- **1.0.0** (2025-11-23): Initial schema documentation
