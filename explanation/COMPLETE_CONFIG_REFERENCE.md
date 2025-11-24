# Complete Configuration Reference Guide

**Version:** 1.0.0
**Last Updated:** 2025-11-23
**Total Config Files:** 29

This document provides a quick reference for all configuration files in the game system.

---

## 📁 **Configuration Directory Structure**

```
config/
├── analytics/        # Analytics & metrics tracking
├── content/          # Game content (levels, arena)
├── core/             # Core entities (characters, items, growth)
├── economy/          # Economy & monetization
├── gameplay/         # Gameplay mechanics
├── liveops/          # Live operations (events, quests)
├── localization/     # Multi-language support
└── system/           # System configuration
```

---

## 🎮 **Gameplay Configs**

### ✅ Skills.json
**Full explanation:** `explanation/gameplay/Skills.md`
- Universal skill system
- Timeline-based execution
- Formula-driven damage
- Status effects (buffs, debuffs, DoT)
- **Key sections:** meta, activation, targeting, timeline, assets

### ✅ BattleRules.json
**Full explanation:** `explanation/gameplay/BattleRules.md`
- Combat system rules
- ATB turn system
- Elemental advantage matrix
- Damage formulas (physical/magical/true)
- Victory/defeat conditions
- **Key sections:** turnSystem, elementalSystem, damageCalculation, resourceSystem

### AI Profiles.json
**Purpose:** AI behavior patterns for Auto-Battle and Enemy AI
- **5 Profiles:** Aggressive DPS, Defensive Support, Tactical Controller, Balanced, Boss
- **Behavior Trees:** Selector, Sequence, Condition nodes
- **Target Priority:** Weight-based selection (LowestHP, HighestThreat, etc.)
- **Skill Usage Rules:** When to use ultimate, when to heal, when to buff
- **Phase-Based AI:** Boss AI changes behavior at HP thresholds

---

## 🏛️ **Core Entity Configs**

### ✅ Characters.json
**Full explanation:** `explanation/core/Characters.md`
- Character definitions
- Base stats + growth curves
- Skill sets (basic, skill1, skill2, ultimate, passive)
- Ascension materials
- Unlock requirements
- **Key sections:** meta, baseStats, growthCurves, skillSet, ascension

### ✅ Items.json
**Full explanation:** `explanation/core/Items.md`
- Equipment, consumables, materials, currencies
- Random stat rolls (min-max ranges)
- Set bonuses
- Enhancement system
- Unique effects
- **Key sections:** meta, baseStats, statRollRange, setBonus, enhancement

### ✅ GrowthCurves.json
**Full explanation:** `explanation/core/GrowthCurves.md`
- Character stat growth formulas
- Level 1 → Max Level scaling
- Breakpoints for validation
- Exponential curves (EXP required, enhancement cost)
- **4 Curves:** Warrior S-Tier, Mage S-Tier, Support A-Tier, Common B-Tier

### ✅ GearSets.json
**Full explanation:** `explanation/core/GearSets.md`
- Equipment set bonuses
- 2-piece and 4-piece effects
- Buffs, debuffs, triggers
- **6 Sets:** Inferno (Fire), Glacier (Ice), Lifesteal (Dark), Speed (Wind), Counter (Physical), Immunity (Light)

---

## 🌍 **Content Configs**

### ✅ Levels.json
**Full explanation:** `explanation/content/Levels.md`
- Campaign chapters and stages
- Enemy waves configuration
- Boss encounters
- 3-star objectives
- Dungeon modes (Gold Vault, EXP Tower)
- **Key sections:** campaigns, stages, dungeons, rewards, drops

### ✅ ArenaConfig.json
**Full explanation:** `explanation/content/ArenaConfig.md`
- Ranked Arena (ELO-based)
- Casual Arena (Power-based)
- Season system (30-day cycles)
- Ranking tiers (Bronze → Grandmaster)
- Ban/pick phase
- Leaderboard rewards
- **Key sections:** matchmaking, rankingSystem, eloSystem, battleRules, rewards

---

## 💰 **Economy Configs**

### ✅ Economy.json
**Full explanation:** `explanation/economy/Economy.md`
- Currency system (Gold, Gems, Arena Coins)
- Resource system (Energy)
- Sources and sinks tracking
- Daily economy targets
- Conversion rates
- Inflation monitoring
- **Key sections:** currencies, resources, conversionRates, dailyEconomy

### ✅ Banners.json
**Full explanation:** `explanation/economy/Banners.md`
- Gacha banner system
- Pity mechanics (soft/hard)
- Pickup guarantees
- Multi-pull discounts
- Drop rate display compliance
- **3 Banners:** Standard, Limited (Fire Dragon), Beginner
- **Key sections:** pity, rates, pool, guarantees, bonuses

### ShopProducts.json
**Purpose:** Shop and IAP product definitions
- **4 Shops:** IAP Gem Store, Arena Shop, Guild Shop, Daily Deals
- **Product Types:** Gem packages, character fragments, materials, bundles
- **Pricing:** Real money (IAP) or soft currency
- **Limits:** Purchase limits, refresh schedules
- **Bonuses:** First purchase bonuses, multi-buy discounts

---

## 🎉 **LiveOps Configs**

### EventSchedule.json
**Purpose:** Live event scheduling and configuration
- **4 Event Types:** Double Drop, Dragon Raid, Login Rewards, Season Pass
- **Scheduling:** Start/end dates, recurrence patterns
- **Event Currency:** Temporary event coins
- **Milestones:** Progressive rewards
- **Global Modifiers:** EXP/Gold multipliers during events

### Quests.json
**Purpose:** Quest and mission system
- **Quest Types:** Daily, weekly, story, event
- **Event Listeners:** Auto-track progress (OnEnemyKilled, OnStageCompleted)
- **Quest Chains:** Unlock next quest on completion
- **Rewards:** Gold, gems, items, character fragments
- **Reset Schedule:** Daily 00:00 UTC, weekly Monday 00:00 UTC

---

## ⚙️ **System Configs**

### SystemSettings.json
**Purpose:** Global game settings
- **Server Config:** API endpoints, timeouts, CDN URLs
- **Client Config:** Version, minimum version, maintenance mode
- **Gameplay Settings:** Team size (5), level cap (80), energy system
- **Performance:** Graphics presets (Low, Medium, High, Ultra)
- **Security:** Anti-cheat, rate limiting
- **Analytics:** Tracking enabled/disabled
- **Localization:** Supported languages (en, zh, ko, ja, es, pt)
- **Regions:** 4 server regions (NA, EU, Asia, SEA)

### FeatureFlags.json
**Purpose:** Feature toggles and A/B testing
- **10 Feature Flags:** PvP Arena, Guild System, Season Pass, Daily Deals, etc.
- **Rollout Strategies:** Percentage-based, whitelist/blacklist, region-based
- **A/B Tests:** Gacha rate test (2% vs 3% SSR)
- **Kill Switches:** Emergency disable features
- **Remote Config:** Update flags without client update

### Matchmaking.json
**Purpose:** Matchmaking algorithms
- **3 Modes:** Ranked (ELO), Casual (Power), Co-op (Party)
- **Match Quality:** Range limits, max search time, bot threshold
- **Anti-Smurf:** Detect new accounts with high win rates
- **Queue Priority:** Premium, streak, wait time
- **Region Locking:** Optional cross-region matching

### Rewards.json
**Purpose:** Drop tables and reward systems
- **Drop Tables:** Chapter 1 normal, Chapter 1 boss, etc.
- **Item Pools:** Rarity weights (SSR 2%, SR 18%, R 80%)
- **Achievements:** 3-star objectives, milestones
- **Level-Up Rewards:** Level 5, 10, 20, 30, 50
- **First-Time Rewards:** Login, stage, gacha, PvP
- **Streak Rewards:** Login streaks (3, 7, 14, 30 days)
- **Random Boxes:** Bronze Box, Gold Box with weighted contents

### SegmentConfig.json
**Purpose:** Player segmentation for personalization
- **Segments:** New Player, Mid-Game, End-Game, Whale, Dolphin, F2P
- **Criteria:** Level, days since install, total spend, activity
- **Offers:** Personalized shop offers per segment
- **Balance:** Different economy targets per segment

### GlobalModifiers.json
**Purpose:** Server-wide temporary modifiers
- **Event Modifiers:** Double EXP, Double Gold, Double Drop
- **Seasonal Bonuses:** Holiday events, anniversary
- **Emergency Nerfs/Buffs:** Quick balance adjustments
- **Duration:** Start/end timestamps, auto-expire

### KillSwitch.json
**Purpose:** Emergency feature disable
- **Kill Switches:** Instantly disable broken features
- **Affected Systems:** Gacha, Arena, Guild, Shop, Events
- **Bypass:** Whitelist for testing
- **Reason:** Store error message for players

### Maintenance.json
**Purpose:** Maintenance mode configuration
- **Scheduled Maintenance:** Weekly Tuesday 02:00-04:00 UTC
- **Emergency Maintenance:** Instant activation
- **Allowed Users:** Dev/QA whitelist for testing
- **Countdown:** Show timer before maintenance
- **Compensation:** Auto-reward after maintenance

### Manifest.json
**Purpose:** Configuration manifest and versioning
- **Config Versions:** Track each config file version
- **CDN URLs:** Where to fetch latest configs
- **Checksums:** Validate config integrity (MD5/SHA256)
- **Dependencies:** Which configs depend on each other
- **Hot-Update:** Which configs can update without restart

---

## 🌍 **Localization Configs**

### Localization.json
**Purpose:** Localization system settings
- **Supported Languages:** en, zh, ko, ja, es, pt (6 languages)
- **Default Language:** en
- **Fallback:** Use English if translation missing
- **RTL Support:** Right-to-left languages (future: Arabic)

### LocalizationKeys.json
**Purpose:** Central key definitions
- **Key Categories:** UI, Character, Item, Skill, Quest, Error
- **Key Format:** CATEGORY_CONTEXT_NAME (e.g., CHAR_NAME_FIRE_DRAGON)
- **Validation:** Ensure all keys exist in language files

### languages/en.json, zh.json, ko.json
**Purpose:** Translation strings
- **English:** Primary language, complete translations
- **Chinese:** Simplified Chinese (zh-CN)
- **Korean:** Korean (ko-KR)
- **Structure:** Nested JSON by category
- **Variables:** Support {0}, {1} placeholders for dynamic text

---

## 📊 **Analytics Config**

### ✅ Analytics.json
**Full explanation:** `explanation/analytics/Analytics.md`
- **15 Metrics:** DAU, MAU, Retention, ARPU, ARPPU, Conversion, Gacha, Currency, Stage, Arena, Crash, Load
- **3 Dashboards:** Overview, Monetization, Economy
- **Storage:** MongoDB collections
- **Alerts:** Threshold-based notifications
- **Export:** Daily CSV export
- **Data Retention:** 30-730 days by metric type

---

## 🔗 **Cross-Reference Map**

### Character → Skills
`Characters.json:skillSet.skill1` → `Skills.json:id`

### Character → Growth Curves
`Characters.json:growthCurves.curveId` → `GrowthCurves.json:id`

### Items → Gear Sets
`Items.json:setBonus.setId` → `GearSets.json:id`

### Banners → Characters
`Banners.json:pool.characterId` → `Characters.json:id`

### Levels → Items (Rewards)
`Levels.json:rewards.itemId` → `Items.json:id`

### Levels → Drop Tables
`Levels.json:drops.table` → `Rewards.json:dropTables.id`

### Arena → Matchmaking
`ArenaConfig.json:matchmaking` → `Matchmaking.json`

### All Systems → Localization
All `nameKey` and `descriptionKey` → `LocalizationKeys.json` → `languages/*.json`

---

## 🎯 **Quick Reference by Use Case**

### Adding a New Character
1. Add entry to `Characters.json`
2. Reference existing skills in `Skills.json` or create new ones
3. Assign growth curve from `GrowthCurves.json`
4. Add ascension materials from `Items.json`
5. Add translations to `languages/*.json`
6. Add to gacha banner in `Banners.json` (optional)

### Creating a New Event
1. Add event to `EventSchedule.json`
2. Create event quest in `Quests.json`
3. Define event shop in `ShopProducts.json`
4. Set event currency in `Economy.json`
5. Add global modifiers in `GlobalModifiers.json` (optional)

### Balancing Combat
1. Adjust formulas in `BattleRules.json`
2. Tune skill damage in `Skills.json`
3. Modify character base stats in `Characters.json`
4. Adjust growth curves in `GrowthCurves.json`
5. Rebalance equipment stats in `Items.json`

### Tuning Economy
1. Adjust sources/sinks in `Economy.json`
2. Modify stage rewards in `Levels.json`
3. Update shop prices in `ShopProducts.json`
4. Change gacha rates in `Banners.json` (requires legal review)
5. Monitor via `Analytics.json` metrics

---

## ✅ **Validation Checklist**

When modifying configs, ensure:

- [ ] JSON syntax is valid (use linter)
- [ ] All ID references exist (no broken links)
- [ ] Localization keys are defined
- [ ] Numeric ranges are reasonable
- [ ] Formulas are mathematically sound
- [ ] Cross-references are consistent
- [ ] Version number is incremented
- [ ] Last updated timestamp is current
- [ ] Changes are documented
- [ ] Tested in dev environment

---

## 📚 **Additional Resources**

- **Architecture Doc:** `GameArchitecture_ConfigDriven.md`
- **Validation Report:** `CONFIGURATION_VALIDATION_REPORT.md`
- **Individual Explanations:** `explanation/` directory
- **Config Files:** `config/` directory

---

**This reference guide covers all 29 configuration files in the X3 game system. For detailed explanations, refer to individual markdown files in the `explanation/` directory.**
