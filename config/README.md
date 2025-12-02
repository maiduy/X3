# Master Configuration System

## Overview

This directory contains the complete master configuration tables for the modular config-driven game architecture. All game systems, content, economy, and analytics are driven by these JSON configuration files, enabling hot-updates and server-side control without client rebuilds.

## Architecture Principles

### Core Philosophy
> **"Code is the Engine, Config is the Fuel"**

- **Decoupling**: Complete separation of game logic (code) and game data (config)
- **Scalability**: Easy addition of new content, game modes, and events
- **Reusability**: Shared engine for multiple RPG/Strategy projects
- **Server Authority**: All critical balance and economy data controlled server-side

### Design Goals

1. **Game Designers** control balance, flow, and events through JSON/Excel
2. **Engineers** build robust data processing systems (Skill Engine, Quest System)
3. **LiveOps** can deploy events, banners, and content updates without client patches

## Directory Structure

```
config/
├── core/                    # Core entity definitions
│   ├── Characters.json      # Character stats, skills, growth curves
│   ├── Characters.schema.md # Character configuration schema documentation
│   ├── GrowthCurves.json    # Stat growth formulas and curves
│   ├── GrowthCurves.schema.md # Growth curve schema documentation
│   ├── GearSets.json        # Equipment set bonuses and effects
│   ├── GearSets.schema.md   # Gear set schema documentation
│   ├── Items.json           # Equipment, consumables, materials
│   └── Items.schema.md      # Item configuration schema documentation
│
├── gameplay/                # Gameplay mechanics
│   ├── Skills.json          # Skill definitions, status effects
│   ├── Skills.schema.md     # Skill system schema documentation
│   ├── BattleRules.json     # Combat system rules, damage formulas
│   ├── BattleRules.schema.md # Battle system schema documentation
│   ├── AIProfiles.json      # AI behavior trees and decision logic
│   └── AIProfiles.schema.md # AI system schema documentation
│
├── content/                 # Game content
│   ├── Levels.json          # PvE campaigns, dungeons
│   ├── Levels.schema.md     # Level/stage schema documentation
│   ├── ArenaConfig.json     # PvP arena, matchmaking, seasons
│   └── ArenaConfig.schema.md # Arena system schema documentation
│
├── economy/                 # Economy & monetization
│   ├── Banners.json         # Gacha banners, rates, pity
│   ├── Banners.schema.md    # Gacha system schema documentation
│   ├── ShopProducts.json    # IAP, shops, currency exchange
│   ├── ShopProducts.schema.md # Shop system schema documentation
│   ├── Economy.json         # Currency balance, sources & sinks
│   └── Economy.schema.md    # Economy schema documentation
│
├── liveops/                 # Live operations
│   ├── EventSchedule.json   # Events, seasons, login rewards
│   ├── EventSchedule.schema.md # Event system schema documentation
│   ├── Quests.json          # Daily, weekly, story quests
│   └── Quests.schema.md     # Quest system schema documentation
│
├── system/                  # System configuration
│   ├── Matchmaking.json     # PvP/PvE matchmaking algorithms
│   ├── Matchmaking.schema.md # Matchmaking schema documentation
│   ├── Rewards.json         # Drop tables, achievements, rewards
│   ├── Rewards.schema.md    # Reward system schema documentation
│   ├── FeatureFlags.json    # Feature toggles, A/B tests
│   ├── FeatureFlags.schema.md # Feature flag schema documentation
│   ├── SystemSettings.json  # Server, client, gameplay settings
│   ├── SystemSettings.schema.md # System settings schema documentation
│   ├── GlobalModifiers.json # Global multipliers and modifiers
│   ├── GlobalModifiers.schema.md # Global modifiers schema documentation
│   ├── KillSwitch.json      # Emergency feature disable switches
│   ├── KillSwitch.schema.md # Kill switch schema documentation
│   ├── Maintenance.json     # Maintenance mode configuration
│   ├── Maintenance.schema.md # Maintenance schema documentation
│   ├── Manifest.json        # Version control and file management
│   ├── Manifest.schema.md   # Manifest schema documentation
│   ├── SegmentConfig.json   # Player segmentation and targeting
│   └── SegmentConfig.schema.md # Segmentation schema documentation
│
├── localization/            # Multi-language support
│   ├── Localization.json    # Localization system configuration
│   ├── Localization.schema.md # Localization schema documentation
│   ├── LocalizationKeys.json # Complete key mapping structure
│   ├── LocalizationKeys.schema.md # Localization keys schema documentation
│   └── languages/           # Language-specific translation files
│       ├── en.json          # English translations
│       ├── zh.json          # Chinese translations
│       └── ko.json          # Korean translations
│
└── analytics/               # Analytics & metrics
    ├── Analytics.json       # Metric definitions, tracking
    ├── Analytics.schema.md  # Analytics schema documentation
    └── analytics_metrics_mongodb.csv # MongoDB ingestion format
```

## Configuration Files

### Core Entities

#### Characters.json
**Schema**: `Characters.schema.md`

Defines all playable characters with:
- **Base Stats**: HP, ATK, DEF, SPD, CRIT, etc.
- **Growth Curves**: References to GrowthCurves.json for level scaling
- **Assets**: Addressable keys for models, animations, VFX
- **Skill Sets**: References to Skills.json
- **Ascension**: Materials and max ascension levels
- **Unlock Requirements**: Gacha, story, or fragment-based
- **Metadata**: Rarity, element, roles, factions, gender, tags

**Use Cases:**
- Adding new characters without code changes
- Balancing character stats via server-side updates
- Managing character progression and ascension
- Cross-referencing with localization for names/descriptions

**Key Features:**
- Multi-role support (characters can have multiple roles)
- Multi-faction support (characters can belong to multiple factions)
- Flexible tagging system for gameplay mechanics
- Localization integration via `nameKey` and `descriptionKey`

#### GrowthCurves.json
**Schema**: `GrowthCurves.schema.md`

Defines stat growth formulas for character progression:
- **Linear Curves**: Constant growth per level
- **Exponential Curves**: Accelerating growth patterns
- **Breakpoint Curves**: Different growth rates at level thresholds
- **Formula Support**: Mathematical expressions for dynamic scaling

**Use Cases:**
- Centralized stat scaling across multiple characters
- Easy balance tuning without touching character configs
- Reusable growth patterns for different character tiers
- Support for complex progression systems (EXP requirements, enhancement costs)

**Key Features:**
- Formula-based stat calculations
- Breakpoint system for tier-based scaling
- Separate curves for different stat types (HP, ATK, DEF, SPD)
- Supports both character stats and economy scaling (EXP, gold costs)

**Example Curves:**
- `CURVE_WARRIOR_S_TIER`: High-end warrior stat scaling for SSR characters
- `CURVE_MAGE_S_TIER`: High-end mage stat scaling for SSR characters
- `CURVE_SUPPORT_A_TIER`: Mid-tier support stat scaling for SR characters
- `CURVE_COMMON_B_TIER`: Basic stat scaling for R characters
- `CURVE_EXP_REQUIRED`: Experience points needed per level
- `CURVE_ENHANCEMENT_COST`: Gold cost for upgrades

#### GearSets.json
**Schema**: `GearSets.schema.md`

Defines equipment set bonuses and synergies:
- **Set Bonuses**: 2-piece, 4-piece, 6-piece bonus tiers
- **Stat Bonuses**: Flat or percentage-based stat increases
- **Special Effects**: Unique mechanics (lifesteal, counter-attack, immunity)
- **Activation Rules**: Minimum pieces required for each bonus tier

**Use Cases:**
- Creating equipment synergies and build diversity
- Encouraging specific gear farming strategies
- Balancing end-game equipment progression
- Cross-referencing with Items.json for set membership

**Key Features:**
- Multi-tier bonus system (progressive rewards for more pieces)
- Mix of stat bonuses and unique effects
- Clear bonus descriptions for player communication
- Integration with localization system

**Example Sets:**
- `SET_INFERNO`: Fire damage and ATK bonuses
- `SET_GLACIER`: Speed reduction and DEF bonuses
- `SET_LIFESTEAL`: HP recovery on attacks
- `SET_SPEED`: SPD and turn manipulation
- `SET_COUNTER`: Counter-attack mechanics
- `SET_IMMUNITY`: Debuff resistance and cleanse

#### Items.json
**Schema**: `Items.schema.md`

Defines equipment, consumables, materials, and currencies:
- **Equipment**: Weapons, armor, accessories with stat rolls
- **Set References**: Links to GearSets.json for set bonuses
- **Consumables**: Potions, buffs with instant effects
- **Materials**: Ascension materials, enhancement ores, fragments
- **Currencies**: Soft, premium, and event currencies
- **Random Stats**: Stat pool configurations for equipment RNG

**Use Cases:**
- Adding new items and equipment sets
- Balancing item stats and drop rates
- Managing economy through currency definitions
- Defining craftable items and enhancement materials

**Key Features:**
- Stack limits for inventory management
- Equipment slot specification (weapon, helmet, armor, boots, accessories)
- Set bonus integration via `setBonusId`
- Stat pool for random substat rolling
- Use effects for consumable items
- Rarity tiers (R, SR, SSR)

### Gameplay Mechanics

#### Skills.json
Universal skill system with:
- **Activation**: Cooldown, cost (mana, energy)
- **Targeting**: Single-target, AoE, area selection
- **Timeline**: Frame-perfect action sequences
- **Effects**: Damage, healing, buffs, debuffs, CC
- **Formulas**: Dynamic damage calculations
- **Status Effects**: DoT, HoT, stat modifiers, CC

**Key Features:**
- Event-driven skill execution
- Formula-based damage (supports caster/target stats)
- Conditional effects (HP thresholds, positioning)
- Multi-hit and delayed effects

#### BattleRules.json
Core combat system configuration:
- **Turn System**: ATB (Active Time Battle) or Turn-Based
- **Elemental System**: Advantage matrix, multipliers
- **Damage Calculation**: Physical, magical, true damage formulas
- **Resource System**: Mana, energy regeneration
- **Victory/Defeat Conditions**: Flexible win/loss conditions

#### AIProfiles.json
AI behavior trees for enemies and auto-battle:
- **Aggressive DPS**: Focus high-value targets
- **Defensive Support**: Healing and buff prioritization
- **Tactical Controller**: CC and debuff strategies
- **Boss Patterns**: Phase-based behavior with scripted actions

### Content

#### Levels.json
PvE campaign and dungeon definitions:
- **Campaigns**: Chapters and stages
- **Enemy Waves**: Composition, levels, positioning
- **Victory Conditions**: Eliminate all, survive turns, kill boss
- **Rewards**: First clear, repeat, 3-star objectives
- **Dungeons**: Resource dungeons with difficulty tiers

#### ArenaConfig.json
PvP arena system:
- **Ranking System**: Bronze to Grandmaster tiers
- **ELO System**: K-factor, placement matches, streak bonuses
- **Seasons**: Duration, rewards, leaderboards
- **Battle Rules**: Team size, ban/pick phase, turn limits
- **Matchmaking**: ELO-based with power consideration

### Economy

#### Banners.json
Gacha system configuration:
- **Banner Types**: Standard, limited, beginner
- **Rates**: SSR/SR/R probabilities
- **Pity System**: Soft/hard pity, carry-over
- **Pickup System**: Featured character guarantees
- **Cost**: Gems, scrolls, multi-pull discounts

**Compliance:**
- Drop rate display for China, Japan, Korea
- Pity tracking and history

#### ShopProducts.json
All shop and IAP products:
- **IAP**: Gem packs, monthly pass, bundles
- **Currency Shops**: Arena coins, guild coins
- **Daily Deals**: Rotating discounted products
- **Refresh Schedules**: Daily, weekly reset times

#### Economy.json
Economy balance and targets:
- **Currency Definitions**: Sources, sinks, caps
- **Balance Targets**: New/mid/end player economy
- **Exchange Rates**: Gem-to-gold, gem-to-energy
- **Inflation Control**: Monthly targets, adjustment methods

### LiveOps

#### EventSchedule.json
Event management:
- **Recurring Events**: Double drop weekends
- **Limited Events**: Raid bosses, special dungeons
- **Seasonal Events**: Season pass, battle pass
- **Login Rewards**: Daily and streak rewards

#### Quests.json
Quest and mission system:
- **Daily Quests**: Stage completion, arena battles
- **Weekly Quests**: Boss hunts, enhancement goals
- **Story Quests**: Campaign progression
- **Event Listeners**: Automatic progress tracking

### System

#### Matchmaking.json
Matchmaking algorithms:
- **Ranked**: ELO-based with range expansion
- **Casual**: Power-based matching
- **Co-op**: Party-based with role balancing
- **Anti-Smurfing**: Detection and acceleration

#### Rewards.json
Reward distribution system:
- **Drop Tables**: Stage and boss loot
- **Achievements**: Progression-based rewards
- **Level-Up Rewards**: Milestone bonuses
- **First-Time Rewards**: Tutorial and onboarding
- **Streak Rewards**: Login and win streaks

#### FeatureFlags.json
Remote configuration and A/B testing:
- **Feature Toggles**: Enable/disable features
- **Rollout Strategies**: Gradual, beta, all users
- **Kill Switches**: Emergency disable
- **A/B Tests**: Variant testing with metrics

#### SystemSettings.json
**Schema**: `SystemSettings.schema.md`

Global system configuration:
- **Server**: API endpoints, timeouts, CDN URLs
- **Client**: Version control, maintenance mode, update policies
- **Gameplay**: Team size, level caps, energy systems, stamina
- **Performance**: Graphics quality presets, frame rate limits, LOD settings
- **Security**: Anti-cheat parameters, rate limiting, encryption settings
- **Network**: Connection timeouts, retry policies, bandwidth limits

**Use Cases:**
- Configuring server environments (dev, staging, production)
- Managing client-side gameplay constants
- Performance optimization per platform
- Security parameter tuning

#### GlobalModifiers.json
**Schema**: `GlobalModifiers.schema.md`

Global game-wide multipliers and modifiers:
- **Economy Modifiers**: Gold gain, EXP gain, drop rate multipliers
- **Event Modifiers**: Temporary double drops, bonus weekends
- **Seasonal Adjustments**: Holiday events, special campaigns
- **Testing Modifiers**: Debug speed multipliers, instant rewards

**Use Cases:**
- Running double EXP/gold events
- Temporary game-wide boosts
- Compensating players during issues
- Testing and debugging game balance

**Key Features:**
- Time-based activation (start/end timestamps)
- Stackable modifiers (multiple can be active)
- Priority system for conflicting modifiers
- Automatic expiration
- Can be updated via hot-patch without client restart

**Example Modifiers:**
```json
{
  "id": "WEEKEND_BONUS",
  "type": "EXP_MULTIPLIER",
  "value": 2.0,
  "startTime": "2025-12-01T00:00:00Z",
  "endTime": "2025-12-03T23:59:59Z",
  "priority": 1
}
```

#### KillSwitch.json
**Schema**: `KillSwitch.schema.md`

Emergency feature disable switches for critical issues:
- **Feature Toggles**: Instantly disable problematic features
- **Character Bans**: Temporarily ban broken characters from use
- **Stage Locks**: Lock specific stages with game-breaking bugs
- **Shop Restrictions**: Disable problematic shop items or purchases
- **Emergency Mode**: Force all players to safe mode

**Use Cases:**
- Emergency response to game-breaking bugs
- Disabling exploitable features immediately
- Preventing economy damage from exploits
- Temporary fixes while deploying proper patches

**Key Features:**
- Hot-patch updates (no client restart required)
- Granular control (disable specific features, not entire systems)
- Audit logging of all kill switch activations
- Player communication integration (display reason messages)
- WhiteList support (allow QA/dev access during kill switch)

**Example Structure:**
```json
{
  "maintenance": false,
  "disabledFeatures": ["GACHA", "ARENA_RANKED"],
  "disabledCharacters": ["CHAR_BROKEN_UNIT"],
  "disabledStages": ["STAGE_EXPLOIT_FARM"],
  "disabledShopItems": ["PACK_BUGGED_ITEM"],
  "whiteListedUsers": ["qa_user_123", "dev_admin_456"],
  "message": "Feature temporarily disabled due to technical issues"
}
```

#### Maintenance.json
**Schema**: `Maintenance.schema.md`

Maintenance mode configuration and scheduling:
- **Scheduled Maintenance**: Planned downtime windows
- **Emergency Maintenance**: Unplanned emergency downtime
- **Maintenance Messages**: Custom messages per language
- **Countdown Timers**: Pre-maintenance warnings
- **Partial Maintenance**: Disable specific features during maintenance

**Use Cases:**
- Scheduling regular server maintenance
- Emergency server shutdowns
- Database migrations
- Major patch deployments
- Communicating maintenance schedules to players

**Key Features:**
- Scheduled maintenance windows with auto-activation
- Pre-maintenance countdown notifications
- Estimated completion time display
- Multi-language maintenance messages
- Compensation item distribution post-maintenance
- Whitelist for staff access during maintenance

**Example Structure:**
```json
{
  "isActive": false,
  "type": "SCHEDULED|EMERGENCY",
  "startTime": "2025-12-01T02:00:00Z",
  "estimatedEndTime": "2025-12-01T06:00:00Z",
  "messages": {
    "en": "Server maintenance in progress. Estimated completion: 6:00 AM UTC",
    "zh": "服务器维护中。预计完成时间：UTC 6:00",
    "ko": "서버 점검 중. 예상 완료 시간: UTC 오전 6시"
  },
  "compensation": {
    "enabled": true,
    "items": [
      {"itemId": "GEM", "quantity": 300},
      {"itemId": "GOLD", "quantity": 50000}
    ]
  }
}
```

#### Manifest.json
**Schema**: `Manifest.schema.md`

Version control and configuration file management:
- **Client Version Requirements**: Minimum/maximum compatible versions
- **Config File Registry**: All config files with versions and checksums
- **CDN Paths**: URLs for downloading config files
- **Forced Updates**: Mandatory client update enforcement
- **File Integrity**: MD5/SHA256 checksums for validation

**Use Cases:**
- Managing config file versions
- Ensuring client-server compatibility
- Preventing tampered config files
- Hot-update deployment tracking
- Rollback to previous config versions

**Key Features:**
- Version checking and compatibility enforcement
- Config file integrity verification (MD5 hashes)
- Critical file marking (must download before game starts)
- Delta updates (only download changed files)
- CDN URL management with fallback mirrors
- Automatic version migration

**Example Structure:**
```json
{
  "minClientVersion": "1.2.0",
  "maxClientVersion": "2.0.0",
  "configVersion": "1.5.3",
  "files": {
    "Characters": {
      "version": "1.0.5",
      "hash": "d41d8cd98f00b204e9800998ecf8427e",
      "url": "https://cdn.example.com/config/Characters.json",
      "critical": true,
      "size": 45678
    },
    "Skills": {
      "version": "1.0.3",
      "hash": "098f6bcd4621d373cade4e832627b4f6",
      "url": "https://cdn.example.com/config/Skills.json",
      "critical": true,
      "size": 123456
    }
  },
  "forceUpdate": false,
  "updateMessage": "New content available! Please update your game."
}
```

#### SegmentConfig.json
**Schema**: `SegmentConfig.schema.md`

Player segmentation and targeting configuration:
- **Player Segments**: New, casual, mid-core, hardcore, whale classifications
- **Behavioral Triggers**: Segment assignment based on player actions
- **Personalized Content**: Different offers/events per segment
- **A/B Test Groups**: Variant assignment for experiments
- **Retention Targeting**: Re-engagement campaigns per segment

**Use Cases:**
- Personalized shop offers based on spending behavior
- Targeted events for different player types
- Retention campaigns for churning players
- A/B testing feature rollouts
- VIP tier management

**Key Features:**
- Dynamic segment assignment based on player behavior
- Multi-dimensional segmentation (spending, playtime, progression)
- Segment-specific content visibility
- Automatic segment migration (e.g., new → casual → mid-core)
- Analytics integration for segment performance tracking

**Example Segments:**
```json
{
  "segments": [
    {
      "id": "WHALE",
      "criteria": {
        "totalSpending": {"min": 1000},
        "loginDays": {"min": 30}
      },
      "benefits": {
        "shopVisibility": ["VIP_PACKS", "EXCLUSIVE_BUNDLES"],
        "eventAccess": ["VIP_RAID", "EXCLUSIVE_GACHA"]
      }
    },
    {
      "id": "NEW_PLAYER",
      "criteria": {
        "accountAge": {"max": 7},
        "level": {"max": 20}
      },
      "benefits": {
        "shopVisibility": ["BEGINNER_PACK"],
        "eventAccess": ["TUTORIAL_MISSIONS"]
      }
    }
  ]
}
```

### Localization

#### Localization.json
**Schema**: `Localization.schema.md`

Localization system configuration and validation rules:
- **Supported Languages**: Language codes (ISO 639-1)
- **Fallback Language**: Default language (English)
- **Validation Rules**: Character limits, parameter matching
- **File Structure**: Translation file organization
- **Update Policy**: Hot-update and versioning strategies

**Use Cases:**
- Configuring multi-language support
- Setting up translation workflows
- Defining validation rules for translations
- Managing language-specific formatting

**Key Features:**
- Dynamic language switching
- Parameter substitution support
- Missing translation fallback
- UTF-8 encoding enforcement
- Translation versioning

#### LocalizationKeys.json
**Schema**: `LocalizationKeys.schema.md`

Complete key mapping structure for all translatable content:
- **13 Categories**: Common, Characters, Skills, Items, GearSets, UI, Stats, Rewards, Notifications, Errors, Battle, Shop, Gacha
- **143 Total Keys**: Organized by functional context
- **Naming Conventions**: SCREAMING_SNAKE_CASE for entities, snake_case for UI
- **Dynamic Parameters**: Parameterized strings for runtime substitution
- **Pattern Definitions**: Clear patterns for adding new keys

**Use Cases:**
- Reference for all localization keys in the game
- Guide for adding new translatable content
- Cross-reference with configuration files (Characters, Skills, Items)
- Translation team documentation

**Key Categories:**
- **characters**: Character names and descriptions (CHAR_NAME_*, CHAR_DESC_*)
- **skills**: Skill names and descriptions (SKILL_NAME_*, SKILL_DESC_*)
- **items**: Item names and descriptions (ITEM_NAME_*, ITEM_DESC_*)
- **gearSets**: Set names and descriptions (SET_NAME_*, SET_DESC_*)
- **ui**: Navigation and menu labels
- **stats**: Stat labels (HP, ATK, DEF, etc.)
- **notifications**: System messages with dynamic parameters {time}, {eventName}
- **gacha**: Summon system labels with {count} parameters

**Integration:**
- Characters.json → references via `meta.nameKey` and `meta.descriptionKey`
- Skills.json → references via `nameKey` and `descriptionKey`
- Items.json → references via `nameKey` and `descriptionKey`
- GearSets.json → references via `nameKey` and `descriptionKey`

#### Language Files (languages/en.json, zh.json, ko.json)
Individual translation files for each supported language:
- **en.json**: English (fallback language)
- **zh.json**: Chinese translations
- **ko.json**: Korean translations

**Structure:**
```json
{
  "version": "1.0.0",
  "language": "en",
  "translations": {
    "characters": {
      "CHAR_NAME_FIRE_DRAGON": "Fire Dragon Warrior",
      "CHAR_DESC_FIRE_DRAGON": "A fierce warrior wielding the power of flames..."
    },
    "common": {
      "ok": "OK",
      "cancel": "Cancel"
    }
  }
}
```

**Use Cases:**
- Storing actual translated text
- Version control for translations
- Hot-update language packs
- Quality assurance testing

### Analytics

#### Analytics.json
**Schema**: `Analytics.schema.md`

Comprehensive metrics tracking:
- **Engagement**: DAU, MAU, session length
- **Retention**: D1, D7, D30 retention
- **Monetization**: ARPU, ARPPU, conversion rate
- **Economy**: Currency flow, gacha pulls
- **Progression**: Stage completion, arena participation
- **Technical**: Crash rate, load times

**Features:**
- Real-time and batch collection
- MongoDB storage with retention policies
- Alert thresholds for critical metrics
- Dashboard definitions

#### analytics_metrics_mongodb.csv
CSV export format for MongoDB ingestion:
- Metric ID, name, description
- Category, priority, collection event
- Frequency, data type, aggregation logic
- Dimensions, database, collection name
- Retention days, alert configuration

**Use Cases:**
- Automated ingestion into analytics pipeline
- Business intelligence tool integration
- Data warehouse ETL processes

## Schema Documentation

Each configuration JSON file has a corresponding `.schema.md` file that provides comprehensive documentation:

### Schema Files Overview

All schema documentation files follow a consistent structure:
1. **Overview**: Purpose and high-level description
2. **Root Level Fields**: Version, metadata, and top-level structures
3. **Field-by-Field Descriptions**: Detailed explanation of each property
4. **Data Types and Formats**: Expected types, enums, and validation rules
5. **Usage Examples**: Real-world configuration examples
6. **Cross-References**: Links to related configuration files
7. **Validation Rules**: Constraints and requirements
8. **Best Practices**: Guidelines for optimal configuration

### Core Schema Files

| Config File | Schema Documentation | Purpose |
|-------------|---------------------|---------|
| Characters.json | Characters.schema.md | Character entity definitions and stats |
| GrowthCurves.json | GrowthCurves.schema.md | Stat scaling formulas and progression curves |
| GearSets.json | GearSets.schema.md | Equipment set bonuses and effects |
| Items.json | Items.schema.md | Items, equipment, materials, currencies |

### Gameplay Schema Files

| Config File | Schema Documentation | Purpose |
|-------------|---------------------|---------|
| Skills.json | Skills.schema.md | Skill mechanics and effect definitions |
| BattleRules.json | BattleRules.schema.md | Combat system rules and formulas |
| AIProfiles.json | AIProfiles.schema.md | AI behavior patterns and decision trees |

### Content Schema Files

| Config File | Schema Documentation | Purpose |
|-------------|---------------------|---------|
| Levels.json | Levels.schema.md | PvE stages, dungeons, and campaigns |
| ArenaConfig.json | ArenaConfig.schema.md | PvP arena system configuration |

### Economy Schema Files

| Config File | Schema Documentation | Purpose |
|-------------|---------------------|---------|
| Banners.json | Banners.schema.md | Gacha system and summon mechanics |
| ShopProducts.json | ShopProducts.schema.md | Shop items and IAP products |
| Economy.json | Economy.schema.md | Currency flow and economic balance |

### LiveOps Schema Files

| Config File | Schema Documentation | Purpose |
|-------------|---------------------|---------|
| EventSchedule.json | EventSchedule.schema.md | Events, seasons, and time-limited content |
| Quests.json | Quests.schema.md | Quest system and mission tracking |

### System Schema Files

| Config File | Schema Documentation | Purpose |
|-------------|---------------------|---------|
| Matchmaking.json | Matchmaking.schema.md | Matchmaking algorithms and rules |
| Rewards.json | Rewards.schema.md | Drop tables and reward distribution |
| FeatureFlags.json | FeatureFlags.schema.md | Feature toggles and A/B testing |
| SystemSettings.json | SystemSettings.schema.md | Global system configuration |
| GlobalModifiers.json | GlobalModifiers.schema.md | Game-wide multipliers and modifiers |
| KillSwitch.json | KillSwitch.schema.md | Emergency feature disable switches |
| Maintenance.json | Maintenance.schema.md | Maintenance mode configuration |
| Manifest.json | Manifest.schema.md | Version control and file management |
| SegmentConfig.json | SegmentConfig.schema.md | Player segmentation and targeting |

### Localization Schema Files

| Config File | Schema Documentation | Purpose |
|-------------|---------------------|---------|
| Localization.json | Localization.schema.md | Localization system configuration |
| LocalizationKeys.json | LocalizationKeys.schema.md | Complete key mapping structure |

### Analytics Schema Files

| Config File | Schema Documentation | Purpose |
|-------------|---------------------|---------|
| Analytics.json | Analytics.schema.md | Metrics tracking and analytics configuration |

### Using Schema Documentation

**For Developers:**
- Reference schema docs when implementing config loaders
- Validate JSON against schema specifications
- Understand data types and constraints
- Follow cross-reference patterns for data integrity

**For Game Designers:**
- Learn what each field controls
- Understand acceptable value ranges
- See examples of valid configurations
- Discover relationships between different configs

**For QA:**
- Validate configurations meet schema requirements
- Test edge cases documented in schema files
- Verify cross-references resolve correctly
- Ensure data integrity across related configs

**For Localizers:**
- Understand localization key patterns
- Learn parameter substitution syntax
- See context for translatable content
- Follow naming conventions

## Cross-Reference Map

Understanding how configuration files reference each other:

```
Characters.json
├── growthCurveId → GrowthCurves.json
├── skillSet.* → Skills.json
├── meta.nameKey → LocalizationKeys.json → languages/*.json
├── meta.descriptionKey → LocalizationKeys.json → languages/*.json
└── ascension.materials[].itemId → Items.json

Items.json
├── equipment.setBonusId → GearSets.json
├── nameKey → LocalizationKeys.json → languages/*.json
└── descriptionKey → LocalizationKeys.json → languages/*.json

GearSets.json
├── nameKey → LocalizationKeys.json → languages/*.json
└── descriptionKey → LocalizationKeys.json → languages/*.json

Skills.json
├── nameKey → LocalizationKeys.json → languages/*.json
├── descriptionKey → LocalizationKeys.json → languages/*.json
└── timeline[].payload.statusEffects[] → (self-referencing)

Levels.json
├── enemies[].characterId → Characters.json
├── rewards.items[].itemId → Items.json
└── unlockRequirements.requiredStage → (self-referencing)

Banners.json
├── cost.itemId → Items.json
├── rewards.characterIds[] → Characters.json
└── pickup.targetId → Characters.json

ShopProducts.json
├── price.currencyId → Items.json (currency type)
├── content[].id → Items.json or Characters.json
└── segment → SegmentConfig.json

Quests.json
├── rewards.items[].itemId → Items.json
├── listener.filter.characterId → Characters.json
└── listener.filter.stageId → Levels.json

EventSchedule.json
├── rewards[].itemId → Items.json
└── modifiers[].target → GlobalModifiers.json

Manifest.json
└── files → All JSON config files (version tracking)
```

## Usage Guidelines

### For Game Designers

1. **Adding New Characters**
   - Add entry to `Characters.json`
   - Define stats, skills (reference `Skills.json`)
   - Upload character assets to CDN
   - Update character fragments in `Items.json`

2. **Balancing Game Economy**
   - Adjust currency sources/sinks in `Economy.json`
   - Modify shop prices in `ShopProducts.json`
   - Update gacha rates in `Banners.json` (with approval)
   - Monitor via `Analytics.json` metrics

3. **Creating Events**
   - Define event in `EventSchedule.json`
   - Create event quests in `Quests.json`
   - Set up event shop in `ShopProducts.json`
   - Configure rewards in `Rewards.json`

### For Engineers

1. **Implementing Config Loading**
   ```csharp
   // Example: Unity C# config loader
   public class ConfigManager {
       public CharacterConfig GetCharacter(string id) {
           return characterConfigs[id];
       }

       public void LoadConfigs() {
           // Load from local or CDN
           LoadJSON<CharacterConfig>("Characters.json");
           LoadJSON<SkillConfig>("Skills.json");
           // Validate references and integrity
       }
   }
   ```

2. **Skill Execution Engine**
   ```csharp
   public class SkillExecutor {
       public void ExecuteSkill(SkillConfig skill, Unit caster, Unit target) {
           foreach (var timelineEvent in skill.timeline) {
               yield return new WaitForSeconds(timelineEvent.time);

               if (timelineEvent.action == "ApplyEffects") {
                   foreach (var effect in timelineEvent.payload) {
                       ApplyEffect(effect, caster, target);
                   }
               }
           }
       }
   }
   ```

3. **Formula Evaluation**
   ```csharp
   public class FormulaEvaluator {
       public float Evaluate(string formula, Unit caster, Unit target) {
           // Parse formula: "(caster.atk * 3.5) + (target.maxHp * 0.05)"
           // Replace variables with actual values
           // Evaluate using expression parser
           return result;
       }
   }
   ```

### For LiveOps

1. **Deploying Events**
   - Update `EventSchedule.json` on CDN
   - Client polls for updates every 5 minutes
   - Event activates at scheduled time
   - Monitor participation in analytics dashboard

2. **Emergency Balance Changes**
   - Update config files on CDN
   - Use `FeatureFlags.json` for instant toggles
   - Kill switch available for critical issues
   - Client updates on next app restart or background refresh

3. **A/B Testing**
   - Define test in `FeatureFlags.json`
   - Configure variants with different configs
   - Track metrics via `Analytics.json`
   - Analyze results in BI dashboard

## Data Pipeline

### Development Workflow

```
Design → Authoring → Validation → Export → Deploy → Runtime
```

1. **Authoring**: Google Sheets/Excel or custom tool
2. **Validation**: CI/CD pipeline checks:
   - JSON schema validation
   - Reference integrity (character IDs exist, skill IDs valid)
   - Balance constraints (drop rates sum to expected values)
3. **Export**: Convert to JSON format
4. **Deploy**: Upload to CDN (AWS S3, CloudFront)
5. **Runtime**: Client downloads on bootstrap, caches locally

### Hot-Update Flow

```
Server Update → CDN Upload → Client Poll → Download → Parse → Apply
```

- **Version Check**: Client checks config version every 5 minutes
- **Delta Updates**: Only download changed files
- **Graceful Degradation**: Fallback to local cache if download fails
- **Validation**: Checksum verification before applying

## Best Practices

### Configuration Design

1. **Use References, Not Duplicates**
   - ✅ `"skillId": "SKILL_PHOENIX_STRIKE"`
   - ❌ Inline skill definition in character config

2. **Formulas Over Hardcoded Values**
   - ✅ `"formula": "caster.atk * 3.5"`
   - ❌ `"damage": 350` (doesn't scale)

3. **Extensibility**
   - Use `"tags": []` for flexible categorization
   - Add `"meta"` blocks for editor-only data
   - Include `"version"` for backward compatibility

4. **Validation Rules**
   - All IDs uppercase with underscores
   - All references must resolve
   - Probabilities must sum correctly
   - Required fields must be present

### Performance Optimization

1. **Addressables**: Use asset keys, not direct references
2. **Lazy Loading**: Load VFX/models only when needed
3. **Memory Management**: Unload unused assets
4. **Caching**: Cache parsed configs, don't re-parse

### Security

1. **Server Authority**: Critical configs (rates, prices) from server
2. **Client Validation**: Verify checksums
3. **Anti-Tamper**: Encrypt sensitive configs
4. **Rate Limiting**: Prevent config spam requests

## Versioning

### Semantic Versioning

```
MAJOR.MINOR.PATCH
1.0.0 → Initial release
1.1.0 → New feature (backward compatible)
1.0.1 → Bug fix
2.0.0 → Breaking change
```

### Migration Strategy

1. **Backward Compatibility**: Support N-1 versions
2. **Deprecation Warnings**: Mark old fields as deprecated
3. **Migration Scripts**: Auto-convert old formats
4. **Gradual Rollout**: Phased deployment

## Troubleshooting

### Common Issues

**Issue**: Config not updating in client
- **Check**: CDN cache TTL
- **Solution**: Invalidate CloudFront cache, force client refresh

**Issue**: Skill not executing
- **Check**: Skill ID exists in Skills.json
- **Check**: Character skillSet references valid skill
- **Solution**: Validate reference integrity in CI

**Issue**: Gacha rates don't match expected
- **Check**: Rates sum correctly (SSR + SR + R = 1.0)
- **Check**: Pickup share calculation
- **Solution**: Use validation tool to check rate tables

## Tools

### Validation Tool
```bash
# Validate all configs
python tools/validate_configs.py --dir config/

# Check specific file
python tools/validate_configs.py --file config/core/Characters.json
```

### Export Tool
```bash
# Export from Google Sheets
python tools/export_sheets.py --sheet-id YOUR_SHEET_ID --output config/

# Export to CDN
aws s3 sync config/ s3://game-configs-bucket/ --acl public-read
```

### Analytics Ingestion
```bash
# Import CSV to MongoDB
mongoimport --db game_analytics --collection metrics \
  --type csv --headerline \
  --file config/analytics/analytics_metrics_mongodb.csv
```

## Support

For questions or issues:
- **Documentation**: See `GameArchitecture_ConfigDriven.md`
- **Examples**: Check Unity sample project
- **Issues**: File ticket in project management system

---

**Version**: 1.1.0
**Last Updated**: 2025-11-29
**Maintained By**: Game Systems Team

## Changelog

### Version 1.1.0 (2025-11-29)
- Added comprehensive documentation for all configuration files
- Added GrowthCurves.json documentation and schema
- Added GearSets.json documentation and schema
- Added Localization system documentation (Localization.json, LocalizationKeys.json, language files)
- Added System configuration files:
  - GlobalModifiers.json - Game-wide multipliers and modifiers
  - KillSwitch.json - Emergency feature disable switches
  - Maintenance.json - Maintenance mode configuration
  - Manifest.json - Version control and file management
  - SegmentConfig.json - Player segmentation and targeting
- Added Schema Documentation section with complete schema file reference tables
- Added Cross-Reference Map showing configuration file relationships
- Updated directory structure to reflect all current files
- Enhanced all configuration file descriptions with detailed use cases and examples

### Version 1.0.0 (2025-11-23)
- Initial release
- Core configuration files documented
- Basic usage guidelines established
