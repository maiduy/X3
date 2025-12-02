# Characters.json Schema Documentation

## Overview
This document provides comprehensive descriptions for all indices (fields) in the `Characters.json` configuration file. This file defines all playable characters in the game with their complete stats, progression systems, and asset references.

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

### `characters` (array)
- **Type**: Array of Character Objects
- **Description**: Complete roster of all playable characters in the game
- **Required**: Yes

---

## Character Object Structure

Each character object in the `characters` array contains the following indices:

### `id` (string)
- **Type**: Unique Identifier
- **Description**: Immutable character identifier used for referencing throughout the codebase
- **Format**: `CHAR_[NAME]_[ARCHETYPE]`
- **Example**: `"CHAR_FIRE_DRAGON"`
- **Required**: Yes
- **Constraints**: Must be unique across all characters

---

## Meta Object (Character Metadata)

### `meta` (object)
Contains display and categorization metadata for the character.

#### `meta.nameKey` (string)
- **Type**: Localization Key
- **Description**: Reference key for character's display name in localization files
- **Format**: `CHAR_NAME_[CHARACTER_ID]`
- **Example**: `"CHAR_NAME_FIRE_DRAGON"`
- **Usage**: Lookup in language files for translated character names

#### `meta.descriptionKey` (string)
- **Type**: Localization Key
- **Description**: Reference key for character's lore/description text
- **Format**: `CHAR_DESC_[CHARACTER_ID]`
- **Example**: `"CHAR_DESC_FIRE_DRAGON"`
- **Usage**: Display character backstory and role description

#### `meta.icon` (string)
- **Type**: Asset Reference
- **Description**: Icon sprite identifier for character portrait in UI
- **Example**: `"Icon_FireDragon_Portrait"`
- **Usage**: Character selection screens, party lists, inventory

#### `meta.model` (string)
- **Type**: Asset Reference
- **Description**: 3D model or sprite identifier for character's in-game representation
- **Example**: `"Model_FireDragon_Character"`
- **Usage**: Battle scenes, character preview, cinematics
- **Note**: References the primary character model asset

#### `meta.rarity` (string)
- **Type**: Enum
- **Description**: Character rarity tier determining base power and acquisition difficulty
- **Values**:
  - `"R"` - Rare (Common)
  - `"SR"` - Super Rare
  - `"SSR"` - Super Super Rare (Legendary)
- **Impact**: Affects gacha rates, base stats, and ascension caps

#### `meta.element` (string)
- **Type**: Enum
- **Description**: Character's elemental affinity affecting damage types and resistances
- **Values**: `"Fire"`, `"Ice"`, `"Light"`, `"Dark"`, `"Nature"`, `"Lightning"`, `"Water"`, `"Earth"`
- **Usage**: Elemental combat system, weakness/resistance calculations

#### `meta.roles` (array of strings)
- **Type**: Array of Enums
- **Description**: Character's combat roles in party composition, allowing multi-role versatility
- **Common Roles**:
  - `"DPS"` - Damage Dealer
  - `"Tank"` - Defensive/Protector
  - `"Support"` - Healer/Buffer/Debuffer
  - `"Sub-DPS"` - Secondary Damage Dealer
  - `"Hybrid"` - Multi-role Versatility
- **Usage**: Team building, AI behavior, recommended party compositions, flexible role assignments

#### `meta.factions` (array of strings)
- **Type**: Array of Categories
- **Description**: Character's lore groups or organization affiliations, allowing multi-faction membership
- **Common Factions**: `"Dragons"`, `"Wizards"`, `"Paladins"`, `"Rogues"`, `"Druids"`, `"Knights"`, `"Scholars"`, `"Mercenaries"`
- **Usage**: Faction bonuses, story missions, synergy effects, cross-faction team compositions

#### `meta.gender` (string)
- **Type**: Enum
- **Description**: Character's gender identity for character design and lore purposes
- **Values**:
  - `"Male"` - Male character
  - `"Female"` - Female character
  - `"Other"` - Non-binary or other gender identity
- **Usage**: Character filtering, voice acting assignments, cosmetic systems, player preferences
- **Required**: Yes

#### `meta.tags` (array of strings)
- **Type**: Array of Keywords
- **Description**: Gameplay characteristic tags for filtering and synergy systems
- **Common Tags**:
  - Combat Style: `"Melee"`, `"Ranged"`
  - Specialty: `"Burst"`, `"Control"`, `"Heal"`, `"Buff"`, `"Debuff"`
  - Target Type: `"AoE"`, `"Single-Target"`
  - Mechanics: `"Stealth"`, `"Defense"`
- **Usage**: Character search filters, skill synergies, achievement tracking

---

## Base Stats Object

### `baseStats` (object)
Defines character's starting statistics at level 1.

#### `baseStats.level` (number)
- **Type**: Integer
- **Description**: Starting level of the character
- **Default**: `1`
- **Range**: Always `1` at base

#### `baseStats.maxLevel` (number)
- **Type**: Integer
- **Description**: Maximum achievable level for this character
- **Default**: `80`
- **Note**: Can be increased through ascension system

#### `baseStats.hp` (number)
- **Type**: Integer
- **Description**: Base health points at level 1
- **Usage**: Character survivability, determines how much damage they can take
- **Typical Range**: 680-1200 based on role

#### `baseStats.atk` (number)
- **Type**: Integer
- **Description**: Base attack power at level 1
- **Usage**: Damage calculation for skills and basic attacks
- **Typical Range**: 70-135 based on role

#### `baseStats.def` (number)
- **Type**: Integer
- **Description**: Base defense value at level 1
- **Usage**: Damage reduction calculation, physical resistance
- **Typical Range**: 50-110 based on role

#### `baseStats.spd` (number)
- **Type**: Integer
- **Description**: Base speed/agility value
- **Usage**: Turn order determination, action frequency in combat
- **Typical Range**: 80-115

#### `baseStats.crit` (number)
- **Type**: Float (Percentage as decimal)
- **Description**: Base critical hit chance
- **Format**: `0.05` = 5%
- **Range**: 0.0 to 1.0
- **Typical Range**: 0.03-0.20

#### `baseStats.critDmg` (number)
- **Type**: Float (Multiplier)
- **Description**: Critical hit damage multiplier
- **Format**: `1.5` = 150% damage (50% bonus)
- **Typical Range**: 1.5-2.0

#### `baseStats.accuracy` (number)
- **Type**: Float (Percentage as decimal)
- **Description**: Base hit chance for attacks and debuffs
- **Format**: `0.95` = 95%
- **Range**: 0.0 to 1.0

#### `baseStats.evasion` (number)
- **Type**: Float (Percentage as decimal)
- **Description**: Base chance to dodge incoming attacks
- **Format**: `0.05` = 5%
- **Range**: 0.0 to 1.0

#### `baseStats.effectRes` (number)
- **Type**: Float (Percentage as decimal)
- **Description**: Base resistance to debuffs and negative status effects
- **Format**: `0.15` = 15%
- **Range**: 0.0 to 1.0

---

## Growth Curve Reference

### `growthCurveId` (string)
- **Type**: Reference ID
- **Description**: References a growth curve definition from `GrowthCurves.json`
- **Format**: `CURVE_[CATEGORY]_[TIER]`
- **Examples**:
  - `"CURVE_WARRIOR_S_TIER"` - High-end warrior stat scaling for SSR characters
  - `"CURVE_MAGE_S_TIER"` - High-end mage stat scaling for SSR characters
  - `"CURVE_SUPPORT_A_TIER"` - Mid-tier support stat scaling for SR characters
  - `"CURVE_COMMON_B_TIER"` - Basic stat scaling for R characters
- **Usage**: Determines how the character's stats (HP, ATK, DEF, SPD) scale from level 1 to max level
- **Benefits**:
  - Reusable growth formulas across multiple characters
  - Centralized balance tuning in `GrowthCurves.json`
  - Supports both linear and exponential growth patterns with breakpoints
- **Required**: Yes
- **Note**: The curve must exist in `GrowthCurves.json` or the game will fail to calculate stats

---

## Assets Object

### `assets` (object)
References to character's visual and audio assets.

#### `assets.prefab` (string)
- **Type**: Addressable Asset Path
- **Description**: Unity prefab path for character's game object
- **Format**: `"Addressables/Characters/[Name]/Prefab"`
- **Usage**: Character instantiation in battle and scenes

#### `assets.animations` (string)
- **Type**: Addressable Asset Path
- **Description**: Animation controller or animation set reference
- **Format**: `"Addressables/Characters/[Name]/Animations"`
- **Usage**: Character movement, attacks, idles, and emotes

#### `assets.portrait` (string)
- **Type**: Addressable Asset Path
- **Description**: High-resolution portrait image for character details
- **Format**: `"Addressables/UI/Portraits/[Name]"`
- **Usage**: Character info screens, gacha results, collection

#### `assets.skillVFX` (string)
- **Type**: Addressable Asset Directory Path
- **Description**: Directory containing all skill visual effects
- **Format**: `"Addressables/VFX/[Name]/"`
- **Usage**: Skill animations, particle effects, impact visuals

---

## Skill Set Object

### `skillSet` (object)
References to character's complete ability loadout.

#### `skillSet.basic` (string)
- **Type**: Skill ID Reference
- **Description**: Basic/auto attack skill identifier
- **Usage**: Default attack, no cooldown or resource cost
- **Example**: `"SKILL_FIRE_CLAW"`

#### `skillSet.skill1` (string)
- **Type**: Skill ID Reference
- **Description**: First active skill with cooldown
- **Usage**: Primary special ability
- **Example**: `"SKILL_FLAME_BREATH"`

#### `skillSet.skill2` (string)
- **Type**: Skill ID Reference
- **Description**: Second active skill with cooldown
- **Usage**: Secondary special ability
- **Example**: `"SKILL_DRAGON_RAGE"`

#### `skillSet.ultimate` (string)
- **Type**: Skill ID Reference
- **Description**: Ultimate/signature move with long cooldown or resource cost
- **Usage**: Most powerful ability, game-changing effect
- **Example**: `"SKILL_PHOENIX_STRIKE"`

#### `skillSet.passive` (string)
- **Type**: Skill ID Reference
- **Description**: Always-active passive ability
- **Usage**: Permanent stat bonuses or conditional effects
- **Example**: `"SKILL_FIRE_MASTERY"`

---

## Unlock Requirements Object

### `unlockRequirements` (object)
Defines how players can obtain this character.

#### `unlockRequirements.defaultUnlocked` (boolean)
- **Type**: Boolean
- **Description**: Whether character is available from the start
- **Values**:
  - `true` - Automatically unlocked
  - `false` - Must be obtained through specified method

#### `unlockRequirements.unlockMethod` (string)
- **Type**: Enum
- **Description**: Primary acquisition method for this character
- **Values**:
  - `"Tutorial"` - Given during tutorial
  - `"Story"` - Unlocked through story progression
  - `"Gacha"` - Obtained through gacha/summoning system
  - `"Event"` - Limited-time event reward
  - `"Shop"` - Purchasable with in-game currency
- **Usage**: Determines unlock flow and player onboarding

#### `unlockRequirements.fragments` (number)
- **Type**: Integer
- **Description**: Number of character fragments needed to unlock
- **Default**: `0` for pre-unlocked characters
- **Usage**: Fragment collection system for gacha characters
- **Typical Values**:
  - `0` - Pre-unlocked
  - `50` - SR characters
  - `80` - SSR characters

---

## Ascension Object

### `ascension` (object)
Character power-up system beyond level cap.

#### `ascension.maxAscension` (number)
- **Type**: Integer
- **Description**: Maximum number of ascension tiers available
- **Range**: 0-6
- **Typical Values**:
  - `5` - Rare (R) characters
  - `6` - SR and SSR characters
- **Usage**: Unlocks additional level cap increases and stat bonuses

#### `ascension.materials` (array)
- **Type**: Array of Material Objects
- **Description**: Resources required for each ascension
- **Note**: This is the base requirement, multiplied per ascension tier

##### Material Object Fields:

###### `itemId` (string)
- **Type**: Item ID Reference
- **Description**: Material item identifier
- **Format**: `"MAT_[TYPE]_[NAME]"`
- **Example**: `"MAT_DRAGON_SCALE"`
- **Usage**: Links to Items.json configuration

###### `count` (number)
- **Type**: Integer
- **Description**: Quantity of this material required
- **Usage**: Resource management, progression gating
- **Typical Range**: 5-20 per material type

---

## Field Naming Conventions

### Localization Keys
- **Format**: `[CONTEXT]_[TYPE]_[IDENTIFIER]`
- **Examples**:
  - `CHAR_NAME_FIRE_DRAGON`
  - `CHAR_DESC_HOLY_KNIGHT`

### Asset References
- **Icons**: `Icon_[Name]_[Type]`
- **Models**: `Model_[Name]_[Type]`

### Skill IDs
- **Format**: `SKILL_[NAME]_[VARIANT]`
- **Example**: `SKILL_FIRE_CLAW`, `SKILL_FLAME_BREATH`

### Material IDs
- **Format**: `MAT_[CATEGORY]_[NAME]`
- **Example**: `MAT_DRAGON_SCALE`, `MAT_FIRE_ESSENCE`

---

## Data Validation Rules

1. **Unique Identifiers**: All `id` fields must be unique across characters
2. **Stat Consistency**: `baseStats` values must match `growthCurves.base` values
3. **Asset Paths**: All asset paths must reference valid Addressable locations
4. **Skill References**: All skill IDs must exist in Skills.json configuration
5. **Material References**: All material IDs must exist in Items.json configuration
6. **Rarity Balance**: Higher rarity should have higher stat totals
7. **Role Appropriateness**: Tank roles should have high HP/DEF, DPS high ATK, etc.

---

## Usage Examples

### Calculating Stats at Level 50
Character stats are calculated using the formula defined in the referenced `growthCurveId`:
```
1. Look up the growth curve in GrowthCurves.json using growthCurveId
2. Apply the formula: stat = baseStats.stat × formula_multiplier
3. Example with CURVE_WARRIOR_S_TIER at level 50:
   hp_at_50 = baseStats.hp × (1 + (50 - 1) × 0.08)
   hp_at_50 = 850 × (1 + 3.92) = 850 × 4.92 = 4,182 HP
```

### Determining Combat Effectiveness
- **High DPS**: Look for high ATK, crit, critDmg stats
- **Tanky**: High HP, DEF, effectRes
- **Fast**: High SPD value, goes first in turn order

### Team Composition
Combine characters with complementary:
- **Roles**: DPS + Tank + Support
- **Elements**: Coverage for enemy weaknesses
- **Tags**: Synergies (e.g., "Burst" + "Buff")

---

## Related Configuration Files

- **GrowthCurves.json**: Defines stat growth formulas referenced in `growthCurveId`
- **Skills.json**: Defines all skill effects referenced in `skillSet`
- **Items.json**: Defines all materials referenced in `ascension.materials`
- **Localization/**: Contains translated text for `nameKey` and `descriptionKey`
- **Addressables/**: Contains all assets referenced in `assets` object

---

## Version History

- **1.0.0** (2025-11-23): Initial schema documentation
