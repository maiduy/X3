# Manifest.json Schema Documentation

## Overview
Defines the master configuration file registry, versioning, update strategies, caching, validation, and rollback mechanisms for all game configuration files. Acts as the central index for configuration management and hot-update system.

---

## Root Level Fields

### `$schema` (string)
- **Type**: URI
- **Description**: JSON Schema version reference for validation
- **Example**: `"https://json-schema.org/draft/2020-12/schema"`

### `manifestVersion` (string)
- **Type**: Semantic Version
- **Description**: Manifest file version for compatibility tracking
- **Format**: `MAJOR.MINOR.PATCH`
- **Example**: `"1.0.0"`
- **Usage**: Track manifest schema changes

### `minClientVersion` (string)
- **Type**: Semantic Version
- **Description**: Minimum client version required for this manifest
- **Format**: `MAJOR.MINOR.PATCH`
- **Example**: `"1.0.0"`
- **Usage**: Version gating for compatibility

### `environment` (string)
- **Values**: `"Development"`, `"Staging"`, `"Production"`
- **Description**: Deployment environment identifier
- **Default**: `"Production"`
- **Usage**: Environment-specific configurations

### `lastUpdated` (string)
- **Type**: ISO 8601 DateTime
- **Description**: Timestamp of last manifest update
- **Format**: `YYYY-MM-DDTHH:mm:ssZ`
- **Example**: `"2025-11-23T00:00:00Z"`

### `cdnBaseUrl` (string)
- **Type**: URL
- **Description**: Base CDN URL for configuration files
- **Example**: `"https://cdn.game.example.com/configs"`
- **Usage**: Prepended to file paths for downloads
- **Required**: Yes

### `files` (object)
- **Type**: File Registry Map
- **Description**: Complete registry of all configuration files
- **Required**: Yes
- **Structure**: `{"fileKey": FileEntry}`

### `updateStrategy` (object)
- **Type**: Update Strategy Configuration
- **Description**: How and when to update configurations
- **Required**: Yes

### `caching` (object)
- **Type**: Caching Configuration
- **Description**: Client-side caching settings
- **Required**: Yes

### `validation` (object)
- **Type**: Validation Configuration
- **Description**: File integrity and validation rules
- **Required**: Yes

### `rollback` (object)
- **Type**: Rollback Configuration
- **Description**: Automatic rollback and version management
- **Required**: Yes

---

## File Entry Object

Each file in the `files` object follows this structure:

### Structure Example
```json
{
  "killSwitch": {
    "path": "system/KillSwitch.json",
    "hash": "d41d8cd98f00b204e9800998ecf8427e",
    "size": 2048,
    "priority": 1,
    "critical": true,
    "description": "Emergency controls and feature gates"
  }
}
```

### `path` (string)
- **Type**: Relative File Path
- **Description**: Relative path from cdnBaseUrl
- **Format**: `category/FileName.json`
- **Example**: `"system/KillSwitch.json"`
- **Required**: Yes
- **Full URL**: `cdnBaseUrl + "/" + path`

### `hash` (string)
- **Type**: MD5 Hash
- **Description**: File content hash for integrity verification
- **Format**: 32-character hexadecimal
- **Example**: `"d41d8cd98f00b204e9800998ecf8427e"`
- **Required**: Yes
- **Usage**: Detect file changes and verify downloads

### `size` (number)
- **Type**: Integer (bytes)
- **Description**: File size in bytes
- **Example**: `2048` (2 KB), `10240` (10 KB)
- **Required**: Yes
- **Usage**: Download progress tracking, storage validation

### `priority` (number)
- **Type**: Integer (1-100)
- **Description**: Download priority (lower = higher priority)
- **Example**: `1` (highest), `10`, `50`, `100` (lowest)
- **Required**: Yes
- **Usage**: Critical files downloaded first

**Priority Tiers**:
- **1-10**: Critical system files (KillSwitch, Maintenance, SystemSettings)
- **11-30**: Core gameplay (Characters, Items, Skills)
- **31-50**: Content (Levels, Events, Quests)
- **51-70**: Economy and analytics
- **71-100**: Optional content

### `critical` (boolean)
- **Description**: Whether file is critical for game launch
- **Default**: `false`
- **Usage**: Must be downloaded before game starts
- **True**: Blocking download (game won't start without it)
- **False**: Can be downloaded in background

### `description` (string)
- **Type**: Display Text
- **Description**: Human-readable file description
- **Example**: `"Character definitions"`
- **Usage**: Documentation and debugging

---

## Files Registry Categories

### System Files (Priority 1-10)

#### `killSwitch`
```json
{
  "path": "system/KillSwitch.json",
  "priority": 1,
  "critical": true,
  "description": "Emergency controls and feature gates"
}
```
**Purpose**: Emergency kill switches, must load first

#### `maintenance`
```json
{
  "path": "system/Maintenance.json",
  "priority": 2,
  "critical": true,
  "description": "Maintenance mode configuration"
}
```
**Purpose**: Check maintenance status before login

#### `systemSettings`
```json
{
  "path": "system/SystemSettings.json",
  "priority": 3,
  "critical": true,
  "description": "Global system configuration"
}
```
**Purpose**: Core system parameters

#### `localization`
```json
{
  "path": "localization/Localization.json",
  "priority": 4,
  "critical": true,
  "description": "Master localization configuration"
}
```
**Purpose**: Localization system initialization

### Core Files (Priority 10-30)

#### `characters`
```json
{
  "path": "core/Characters.json",
  "priority": 10,
  "critical": false,
  "description": "Character definitions"
}
```

#### `items`
```json
{
  "path": "core/Items.json",
  "priority": 11,
  "critical": false,
  "description": "Item and equipment definitions"
}
```

#### `skills`
```json
{
  "path": "gameplay/Skills.json",
  "priority": 20,
  "critical": false,
  "description": "Skill and status effect definitions"
}
```

#### `battleRules`
```json
{
  "path": "gameplay/BattleRules.json",
  "priority": 21,
  "critical": false,
  "description": "Combat system rules"
}
```

### Content Files (Priority 30-50)

#### `levels`
```json
{
  "path": "content/Levels.json",
  "priority": 30,
  "critical": false,
  "description": "PvE campaign and dungeon configurations"
}
```

#### `arenaConfig`
```json
{
  "path": "content/ArenaConfig.json",
  "priority": 31,
  "critical": false,
  "description": "PvP arena configuration"
}
```

#### `eventSchedule`
```json
{
  "path": "liveops/EventSchedule.json",
  "priority": 50,
  "critical": false,
  "description": "Event scheduling and configuration"
}
```

### Economy Files (Priority 40-50)

#### `banners`
```json
{
  "path": "economy/Banners.json",
  "priority": 40,
  "critical": false,
  "description": "Gacha banner configurations"
}
```

#### `shopProducts`
```json
{
  "path": "economy/ShopProducts.json",
  "priority": 41,
  "critical": false,
  "description": "Shop and IAP products"
}
```

### System Support Files (Priority 60-70)

#### `matchmaking`
```json
{
  "path": "system/Matchmaking.json",
  "priority": 60,
  "critical": false,
  "description": "Matchmaking algorithms"
}
```

#### `featureFlags`
```json
{
  "path": "system/FeatureFlags.json",
  "priority": 62,
  "critical": false,
  "description": "Feature toggles and A/B tests"
}
```

#### `globalModifiers`
```json
{
  "path": "system/GlobalModifiers.json",
  "priority": 64,
  "critical": false,
  "description": "Global game modifiers for events"
}
```

### Analytics Files (Priority 70+)

#### `analytics`
```json
{
  "path": "analytics/Analytics.json",
  "priority": 70,
  "critical": false,
  "description": "Analytics metrics and tracking"
}
```

---

## Update Strategy Object

### `type` (string)
- **Values**: `"Incremental"`, `"Full"`, `"OnDemand"`
- **Description**: Configuration update strategy
- **Default**: `"Incremental"`

**Strategy Types**:
- **Incremental**: Only download changed files (check hash)
- **Full**: Download all files every time
- **OnDemand**: Download files only when needed

### `checkInterval` (number)
- **Type**: Integer (seconds)
- **Description**: Interval between update checks
- **Default**: `300` (5 minutes)
- **Usage**: Background polling frequency
- **Example**: `300` = check every 5 minutes, `3600` = check every hour

### `forceUpdateOnLaunch` (boolean)
- **Description**: Check for updates on every game launch
- **Default**: `false`
- **True**: Always check on launch (slower but current)
- **False**: Use cached files if within checkInterval

### `allowBackgroundUpdate` (boolean)
- **Description**: Allow updates while game is running
- **Default**: `true`
- **True**: Hot-reload configurations during gameplay
- **False**: Only update on launch or restart

### `maxRetryAttempts` (number)
- **Type**: Integer
- **Description**: Maximum download retry attempts
- **Default**: `3`
- **Usage**: Network failure resilience

### `retryDelay` (number)
- **Type**: Integer (milliseconds)
- **Description**: Delay between retry attempts
- **Default**: `5000` (5 seconds)
- **Example**: `5000` = 5 seconds, `30000` = 30 seconds

---

## Caching Object

### `enabled` (boolean)
- **Description**: Enable client-side caching
- **Default**: `true`
- **Usage**: Offline play support

### `maxCacheSize` (number)
- **Type**: Integer (bytes)
- **Description**: Maximum total cache size
- **Default**: `52428800` (50 MB)
- **Usage**: Storage limit enforcement
- **Example**: `52428800` = 50 MB, `104857600` = 100 MB

### `cacheDuration` (number)
- **Type**: Integer (seconds)
- **Description**: How long cached files are valid
- **Default**: `86400` (24 hours)
- **Usage**: Cache expiration
- **Example**: `86400` = 1 day, `604800` = 7 days

### `compressionEnabled` (boolean)
- **Description**: Enable compression for cached files
- **Default**: `true`
- **Usage**: Reduce storage usage
- **True**: Compress with gzip/deflate
- **False**: Store uncompressed

---

## Validation Object

### `verifyHash` (boolean)
- **Description**: Verify file hash after download
- **Default**: `true`
- **Usage**: Integrity checking
- **Effect**: Reject files with mismatched hash

### `verifySignature` (boolean)
- **Description**: Verify cryptographic signature
- **Default**: `false`
- **Usage**: Enhanced security (requires signing infrastructure)
- **True**: Reject unsigned or invalid signatures
- **False**: Skip signature verification

### `allowFallbackToCache` (boolean)
- **Description**: Use cached files if download fails
- **Default**: `true`
- **Usage**: Offline mode support
- **True**: Use cache if network unavailable
- **False**: Require fresh download

### `strictMode` (boolean)
- **Description**: Strict validation (fail on any error)
- **Default**: `false`
- **Usage**: Development/testing
- **True**: Reject any validation errors
- **False**: Tolerate minor issues

---

## Rollback Object

### `enabled` (boolean)
- **Description**: Enable automatic rollback
- **Default**: `true`
- **Usage**: Safety mechanism for bad configs

### `keepPreviousVersions` (number)
- **Type**: Integer
- **Description**: Number of previous versions to keep
- **Default**: `3`
- **Usage**: Version history depth
- **Example**: `3` = keep last 3 versions

### `autoRollbackOnError` (boolean)
- **Description**: Automatically rollback on error
- **Default**: `true`
- **Usage**: Automatic recovery
- **True**: Rollback to previous version on parse/validation error
- **False**: Manual rollback only

---

## Update Workflow

### Initial Download (First Launch)
```
1. Fetch Manifest.json
2. Parse file registry
3. Sort files by priority
4. Download critical files (critical=true) sequentially
5. Validate each file (hash check)
6. Parse and apply configurations
7. Start game
8. Download non-critical files in background
9. Cache all files locally
```

### Incremental Update (Subsequent Launches)
```
1. Load cached Manifest.json
2. Fetch latest Manifest.json from CDN
3. Compare manifestVersion
4. If versions differ:
   a. Compare file hashes
   b. Identify changed files
   c. Download only changed files (by priority)
   d. Validate downloads
   e. Update cache
   f. Apply new configurations
5. If versions same:
   a. Use cached files
   b. No download needed
```

### Background Update (During Gameplay)
```
Interval: Every checkInterval seconds
Process:
1. Check if allowBackgroundUpdate = true
2. Fetch latest Manifest.json
3. If changes detected:
   a. Download changed files
   b. Validate files
   c. Hot-reload compatible configs
   d. Flag incompatible configs for next restart
4. Continue gameplay
```

### Error Recovery
```
Download Error:
1. Attempt retry (up to maxRetryAttempts)
2. Wait retryDelay between attempts
3. If all retries fail:
   a. If allowFallbackToCache = true:
      - Use cached version
      - Log warning
   b. If allowFallbackToCache = false:
      - Show error to player
      - Request manual retry

Parse Error:
1. Detect JSON parse error
2. If autoRollbackOnError = true:
   a. Rollback to previous version
   b. Load previous config
   c. Log error for debugging
3. If autoRollbackOnError = false:
   a. Show error
   b. Request manual intervention
```

---

## Hash Verification Process

### MD5 Hash Calculation
```
1. Download file content
2. Calculate MD5 hash of content
3. Compare with manifest hash
4. If match:
   - File valid
   - Proceed with parsing
5. If mismatch:
   - File corrupted or modified
   - Reject file
   - Retry download or use cached version
```

### Example Hash Check
```
Downloaded file: Characters.json
Content hash: 7d793037a0760186574b0282f2f435e7
Manifest hash: 7d793037a0760186574b0282f2f435e7
Result: MATCH → Valid file

Downloaded file: Items.json
Content hash: 8277e0910d750195b448797616e091ad
Manifest hash: 1234567890abcdef1234567890abcdef
Result: MISMATCH → Reject and retry
```

---

## Priority-Based Download Order

### Download Sequence Example
```
Critical files (blocking, sequential):
1. Priority 1: KillSwitch.json
2. Priority 2: Maintenance.json
3. Priority 3: SystemSettings.json
4. Priority 4: Localization.json
5. Priority 5: LocalizationKeys.json
6. Priority 6: en.json (English)

→ Game can now start

Non-critical files (background, parallel):
7. Priority 10: Characters.json
8. Priority 11: Items.json
9. Priority 12: GrowthCurves.json
10. Priority 13: GearSets.json
... (continue by priority)

→ Files loaded as needed, game already playable
```

---

## CDN URL Construction

### Base URL
```
cdnBaseUrl: "https://cdn.game.example.com/configs"
```

### File URL Construction
```
File entry:
{
  "path": "system/KillSwitch.json"
}

Full URL:
https://cdn.game.example.com/configs/system/KillSwitch.json

Download request:
GET https://cdn.game.example.com/configs/system/KillSwitch.json
```

### Environment-Specific URLs
```
Development:
cdnBaseUrl: "https://cdn-dev.game.example.com/configs"

Staging:
cdnBaseUrl: "https://cdn-staging.game.example.com/configs"

Production:
cdnBaseUrl: "https://cdn.game.example.com/configs"
```

---

## Cache Management

### Storage Structure
```
Local cache directory:
/cache/configs/

Files:
/cache/configs/manifest.json (cached manifest)
/cache/configs/system/KillSwitch.json
/cache/configs/system/Maintenance.json
/cache/configs/core/Characters.json
... (all cached files)

Metadata:
/cache/configs/.meta (cache metadata)
- File timestamps
- Cache version
- Total cache size
```

### Cache Expiration
```
File cached at: 2025-12-01 00:00:00
cacheDuration: 86400 seconds (24 hours)
Expires at: 2025-12-02 00:00:00

Current time: 2025-12-01 12:00:00
Status: Valid (within cacheDuration)

Current time: 2025-12-02 01:00:00
Status: Expired (beyond cacheDuration)
Action: Fetch fresh version
```

### Cache Size Management
```
maxCacheSize: 52428800 bytes (50 MB)
Current cache size: 48000000 bytes (45.7 MB)
Status: Within limit

New file download: 6000000 bytes (5.7 MB)
Projected total: 54000000 bytes (51.4 MB)
Status: EXCEEDS LIMIT

Action:
1. Remove oldest cached files
2. Keep critical files
3. Make space for new file
4. Download new file
```

---

## Rollback Scenarios

### Scenario 1: Bad Configuration Deployed
```
Version 1.0.0: Working
Version 1.1.0: Deployed with bug (syntax error)

Rollback process:
1. Client downloads 1.1.0 manifest
2. Downloads Characters.json v1.1.0
3. Parse error detected (invalid JSON)
4. autoRollbackOnError = true
5. Rollback to keepPreviousVersions[0] (v1.0.0)
6. Load Characters.json v1.0.0 from cache
7. Game continues with v1.0.0
8. Report error to operations team
```

### Scenario 2: Network Failure
```
Current version: 1.0.0 (cached)
Attempted update: 1.1.0 (network timeout)

Recovery:
1. Download attempt fails (3 retries)
2. allowFallbackToCache = true
3. Use cached v1.0.0
4. Game continues with v1.0.0
5. Retry update in next checkInterval
```

### Scenario 3: Critical File Corrupted
```
Download: KillSwitch.json
Hash check: FAILED (corrupted during transfer)

Recovery:
1. Hash mismatch detected
2. Retry download (attempt 2 of 3)
3. Download succeeds
4. Hash verified
5. Apply configuration
```

---

## Hot-Reload Support

### Hot-Reloadable Files
```
Can be updated during gameplay:
- FeatureFlags.json (feature toggles)
- GlobalModifiers.json (event modifiers)
- EventSchedule.json (event timing)
- KillSwitch.json (emergency controls)
- Maintenance.json (maintenance schedule)
```

### Non-Hot-Reloadable Files
```
Require restart:
- Characters.json (character definitions)
- Items.json (item definitions)
- BattleRules.json (combat rules)
- SystemSettings.json (core settings)

Reason: Deep integration, reload = game restart
```

### Hot-Reload Process
```
1. Background update detects FeatureFlags.json change
2. Download new version
3. Validate file
4. Parse JSON
5. Apply new feature states
6. Update UI (show/hide features)
7. Continue gameplay (no restart)
```

---

## Version Compatibility

### Manifest Version Changes
```
Client v1.0.0 supports:
- manifestVersion: 1.0.0
- minClientVersion: 1.0.0

Manifest updated to 1.1.0:
- New optional fields added
- Backward compatible
- Client v1.0.0 can still use it

Manifest updated to 2.0.0:
- Breaking changes
- minClientVersion: 2.0.0
- Client v1.0.0 CANNOT use it
- Force update required
```

### Client Version Gating
```
Client version: 1.5.0
Manifest minClientVersion: 1.0.0
Result: Compatible, proceed

Client version: 0.9.0
Manifest minClientVersion: 1.0.0
Result: INCOMPATIBLE, force update required
```

---

## Data Validation Rules

1. **Manifest Version**:
   - manifestVersion format: MAJOR.MINOR.PATCH
   - minClientVersion ≤ current manifestVersion

2. **File Entries**:
   - All paths must be unique
   - Hash must be valid MD5 (32 hex characters)
   - Size must be > 0
   - Priority must be 1-100

3. **Priority Consistency**:
   - critical=true should have priority ≤ 10
   - No duplicate priorities (recommended)

4. **CDN URL**:
   - Must be valid HTTPS URL
   - Should not end with `/`

5. **Update Strategy**:
   - checkInterval: 60-3600 seconds (reasonable range)
   - maxRetryAttempts: 1-10
   - retryDelay: 1000-60000 milliseconds

6. **Caching**:
   - maxCacheSize: > sum of all critical file sizes
   - cacheDuration: 3600-604800 seconds (1 hour - 7 days)

7. **Rollback**:
   - keepPreviousVersions: 1-10 (reasonable range)

---

## Design Guidelines

### Priority Assignment
- **1-3**: Emergency and critical system files
- **4-10**: Essential startup files
- **11-30**: Core gameplay content
- **31-50**: Secondary content
- **51-100**: Optional/analytics files

### Hash Management
- Recalculate hash on every file change
- Automate hash generation in build pipeline
- Validate hashes in staging before production

### Cache Strategy
- Set cacheDuration based on update frequency
- Critical files: longer cache (reduces load)
- Event files: shorter cache (stay current)

### Update Timing
- checkInterval: Balance currency vs server load
- 5 minutes: Good for live ops
- 1 hour: Sufficient for stable games

### Rollback Safety
- Always keep ≥ 3 previous versions
- Test rollback in staging
- Monitor rollback frequency (high = bad configs)

### CDN Optimization
- Use CDN with global distribution
- Enable compression (gzip)
- Set appropriate cache headers
- Monitor CDN costs

---

## Monitoring and Analytics

### Metrics to Track
```
- Manifest fetch success rate
- File download success rate
- Average download time per file
- Hash mismatch frequency
- Rollback frequency
- Cache hit rate
- Update check frequency
- Network failure rate
```

### Alerts
```
Critical:
- Manifest fetch failure > 5%
- Hash mismatch rate > 1%
- Rollback frequency > 10/hour

Warning:
- Download time > 10 seconds
- Cache size > 90% of max
- Retry rate > 20%
```

---

## Related Configuration Files

- **KillSwitch.json**: Emergency controls (priority 1)
- **Maintenance.json**: Maintenance scheduling (priority 2)
- **SystemSettings.json**: Core system config (priority 3)
- **FeatureFlags.json**: Hot-reloadable feature toggles
- **All other .json files**: Managed by this manifest

---

## Version History
- **1.0.0** (2025-11-23): Initial schema documentation
