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
│   └── Items.json          # Equipment, consumables, materials
│
├── gameplay/               # Gameplay mechanics
│   ├── Skills.json        # Skill definitions, status effects
│   ├── BattleRules.json   # Combat system rules, damage formulas
│   └── AIProfiles.json    # AI behavior trees and decision logic
│
├── content/               # Game content
│   ├── Levels.json       # PvE campaigns, dungeons
│   └── ArenaConfig.json  # PvP arena, matchmaking, seasons
│
├── economy/              # Economy & monetization
│   ├── Banners.json     # Gacha banners, rates, pity
│   ├── ShopProducts.json # IAP, shops, currency exchange
│   └── Economy.json     # Currency balance, sources & sinks
│
├── liveops/             # Live operations
│   ├── EventSchedule.json # Events, seasons, login rewards
│   └── Quests.json       # Daily, weekly, story quests
│
├── system/              # System configuration
│   ├── Matchmaking.json    # PvP/PvE matchmaking algorithms
│   ├── Rewards.json        # Drop tables, achievements, rewards
│   ├── FeatureFlags.json   # Feature toggles, A/B tests
│   └── SystemSettings.json # Server, client, gameplay settings
│
└── analytics/           # Analytics & metrics
    ├── Analytics.json   # Metric definitions, tracking
    └── analytics_metrics_mongodb.csv # MongoDB ingestion format
```

## Configuration Files

### Core Entities

#### Characters.json
Defines all playable characters with:
- **Base Stats**: HP, ATK, DEF, SPD, CRIT, etc.
- **Growth Curves**: Level scaling formulas
- **Assets**: Addressable keys for models, animations, VFX
- **Skill Sets**: References to Skills.json
- **Ascension**: Materials and max ascension levels
- **Unlock Requirements**: Gacha, story, or fragment-based

**Use Cases:**
- Adding new characters without code changes
- Balancing character stats via server-side updates
- Managing character progression and ascension

#### Items.json
Defines equipment, consumables, materials, and currencies:
- **Equipment**: Weapons, armor, accessories with stat rolls
- **Set Bonuses**: 2-piece and 4-piece set effects
- **Consumables**: Potions, buffs with instant effects
- **Materials**: Ascension materials, enhancement ores
- **Currencies**: Soft, premium, and event currencies

**Use Cases:**
- Adding new items and equipment sets
- Balancing item stats and drop rates
- Managing economy through currency definitions

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
Global system configuration:
- **Server**: API endpoints, timeouts
- **Client**: Version control, maintenance mode
- **Gameplay**: Team size, level caps, energy
- **Performance**: Graphics quality, frame rate
- **Security**: Anti-cheat, rate limiting

### Analytics

#### Analytics.json
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

**Version**: 1.0.0
**Last Updated**: 2025-11-23
**Maintained By**: Game Systems Team
