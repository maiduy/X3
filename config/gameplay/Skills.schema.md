# Skills.json Schema Documentation

## Overview
This document provides comprehensive descriptions for all indices (fields) in the `Skills.json` configuration file. This file defines all character abilities, their effects, targeting rules, visual timelines, and associated status effects.

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

### `skills` (array)
- **Type**: Array of Skill Objects
- **Description**: Complete collection of all skills, abilities, and attacks in the game
- **Required**: Yes

### `statusEffects` (array)
- **Type**: Array of Status Effect Objects
- **Description**: All buffs, debuffs, and status conditions that can be applied to units
- **Required**: Yes

---

## Skill Object Structure

### `id` (string)
- **Type**: Unique Identifier
- **Description**: Immutable skill identifier used for referencing in Characters.json
- **Format**: `SKILL_[NAME]_[VARIANT]`
- **Example**: `"SKILL_PHOENIX_STRIKE"`
- **Required**: Yes
- **Constraints**: Must be unique across all skills

---

## Meta Object (Skill Metadata)

### `meta` (object)
Contains display and categorization metadata for the skill.

#### `meta.nameKey` (string)
- **Type**: Localization Key
- **Description**: Reference key for skill's display name
- **Format**: `SKILL_NAME_[SKILL_ID]`
- **Example**: `"SKILL_NAME_PHOENIX_STRIKE"`

#### `meta.descriptionKey` (string)
- **Type**: Localization Key
- **Description**: Reference key for skill's effect description
- **Format**: `SKILL_DESC_[SKILL_ID]`
- **Example**: `"SKILL_DESC_PHOENIX_STRIKE"`
- **Usage**: Player-facing tooltip text

#### `meta.icon` (string)
- **Type**: Asset Reference
- **Description**: Icon sprite identifier for skill in UI
- **Example**: `"Icon_Skill_PhoenixStrike"`

#### `meta.tags` (array of strings)
- **Type**: Array of Keywords
- **Description**: Categorical tags for skill properties
- **Common Tags**:
  - **Element**: `"Fire"`, `"Ice"`, `"Light"`, `"Dark"`, `"Nature"`, `"Physical"`
  - **Range**: `"Melee"`, `"Ranged"`, `"AoE"`, `"Single-Target"`
  - **Type**: `"Burst"`, `"Control"`, `"Heal"`, `"Buff"`, `"Debuff"`, `"Execute"`
  - **Category**: `"Basic"`, `"Active"`, `"Ultimate"`, `"Passive"`
- **Usage**: Skill filters, synergy calculations, AI decision-making

---

## Activation Object

### `activation` (object)
Defines skill usage requirements and restrictions.

#### `activation.cooldown` (number)
- **Type**: Float (seconds)
- **Description**: Time in seconds before skill can be used again after activation
- **Default**: `0.0` for passive skills
- **Usage**: Combat pacing, skill rotation planning
- **Example**: `12.0` = 12-second cooldown

#### `activation.initialCooldown` (number)
- **Type**: Float (seconds)
- **Description**: Starting cooldown at battle start (prevents immediate use)
- **Default**: `0.0` (can use immediately)
- **Usage**: Balance powerful skills, prevent first-turn dominance
- **Example**: `0.0` = available turn 1

#### `activation.cost` (array)
- **Type**: Array of Resource Cost Objects
- **Description**: Resources consumed when using the skill
- **Can be empty**: Yes (for passive or free skills)

##### Cost Object Fields:

###### `resource` (string)
- **Type**: Resource Type Enum
- **Description**: Type of resource consumed
- **Values**: `"Mana"`, `"Energy"`, `"HP"`, `"Rage"`, `"Focus"`
- **Example**: `"Mana"`

###### `value` (number)
- **Type**: Integer
- **Description**: Amount of resource consumed
- **Range**: 0-100+
- **Example**: `50`

---

## Targeting Object

### `targeting` (object)
Defines how the skill selects targets.

#### `targeting.mode` (string)
- **Type**: Enum
- **Description**: Targeting behavior type
- **Values**:
  - `"Self"` - Only caster
  - `"TargetUnit"` - Player selects single target
  - `"TargetArea"` - Player selects location
  - `"AutoTarget"` - AI selects based on filter
  - `"AllAllies"` - Entire friendly team
  - `"AllEnemies"` - Entire enemy team

#### `targeting.range` (number)
- **Type**: Float (units)
- **Description**: Maximum distance from caster to target
- **Default**: `0.0` for self-cast
- **Usage**: Tactical positioning, range limitations
- **Example**: `4.0` = 4 unit range

#### `targeting.filter` (object | null)
- **Type**: Filter Configuration Object
- **Description**: Rules for automatic target selection
- **Null**: For self-cast or manual selection

##### Filter Object Fields:

###### `filter.team` (string)
- **Type**: Enum
- **Description**: Team affiliation of valid targets
- **Values**: `"Enemy"`, `"Ally"`, `"Self"`, `"Any"`

###### `filter.sort` (string)
- **Type**: Enum
- **Description**: Priority sorting for target selection
- **Values**:
  - `"LowestHP"` - Target with least HP
  - `"HighestHP"` - Target with most HP
  - `"Nearest"` - Closest target
  - `"Farthest"` - Most distant target
  - `"Random"` - Random selection
  - `"LowestHPPercent"` - Target with lowest HP %

###### `filter.maxTargets` (number)
- **Type**: Integer
- **Description**: Maximum number of targets affected
- **Range**: 1+ (1 for single-target)
- **Example**: `1`

#### `targeting.aoe` (object)
- **Type**: AoE Configuration Object
- **Description**: Area of effect parameters

##### `aoe.enabled` (boolean)
- **Type**: Boolean
- **Description**: Whether skill has area of effect
- **Values**: `true` or `false`

##### `aoe.shape` (string)
- **Type**: Enum
- **Description**: Geometric shape of AoE
- **Values**: `"Circle"`, `"Cone"`, `"Line"`, `"Rectangle"`
- **Example**: `"Circle"`

##### `aoe.radius` (number)
- **Type**: Float (units)
- **Description**: Size of AoE from center
- **Example**: `2.0` = 2 unit radius

##### `aoe.centerOn` (string)
- **Type**: Enum
- **Description**: Where AoE is centered
- **Values**:
  - `"Target"` - Primary target location
  - `"Caster"` - Caster's position
  - `"TargetLocation"` - Player-selected ground location

---

## Timeline Array

### `timeline` (array)
- **Type**: Array of Timeline Event Objects
- **Description**: Chronological sequence of skill execution events
- **Usage**: Animation synchronization, damage application timing, VFX spawning

### Timeline Event Object

#### `time` (number)
- **Type**: Float (seconds)
- **Description**: Time offset from skill activation when this event occurs
- **Example**: `0.5` = execute 0.5 seconds after skill start
- **Range**: 0.0+

#### `action` (string)
- **Type**: Action Type Enum
- **Description**: Type of event to execute
- **Values**:
  - `"PlayAnim"` - Play character animation
  - `"SpawnVFX"` - Create visual effect
  - `"ApplyEffects"` - Apply damage/healing/status
  - `"ApplyEffectsOverTime"` - Repeated effect application
  - `"Teleport"` - Move character position
  - `"CameraShake"` - Screen shake effect
  - `"ApplyPassiveBuff"` - Permanent passive effect

#### `param` (string | object)
- **Type**: Mixed
- **Description**: Parameters specific to the action type
- **Examples**:
  - String: `"Attack_Ultimate_Phoenix"` for animation name
  - String: `"VFX_Phoenix_Charge"` for VFX prefab
  - String: `"BehindTarget"` for teleport location
  - Object: `{"intensity": 0.8, "duration": 0.3}` for camera shake

#### `target` (string)
- **Type**: Target Reference Enum
- **Description**: Who/where the action affects
- **Values**: `"Caster"`, `"Target"`, `"TargetArea"`, `"DamageTarget"`
- **Optional**: Only for relevant actions

#### `duration` (number)
- **Type**: Float (seconds)
- **Description**: How long the action persists (for VFX, DoTs)
- **Optional**: Only for time-based actions
- **Example**: `5.0`

#### `interval` (number)
- **Type**: Float (seconds)
- **Description**: Time between repeated effect applications
- **Usage**: Damage-over-time, heal-over-time ticks
- **Example**: `1.0` = once per second

#### `tickCount` (number)
- **Type**: Integer
- **Description**: Number of times to apply repeated effects
- **Usage**: DoT duration control
- **Example**: `5` = applies 5 times

#### `payload` (array)
- **Type**: Array of Effect Objects
- **Description**: Effects to apply when this timeline event executes
- **Usage**: Damage, healing, status application, buffs/debuffs

---

## Effect Payload Objects

### Damage Effect

#### `type: "Damage"`
Applies damage to target(s).

##### `formula` (string)
- **Type**: Damage Calculation Formula
- **Description**: Expression for calculating damage amount
- **Variables**:
  - `caster.atk` - Caster's attack stat
  - `caster.def` - Caster's defense stat
  - `target.maxHp` - Target's maximum HP
  - `target.currentHp` - Target's current HP
- **Operators**: `*`, `+`, `-`, `/`, `()`
- **Example**: `"(caster.atk * 3.5) + (target.maxHp * 0.05)"`

##### `element` (string)
- **Type**: Enum
- **Description**: Elemental damage type
- **Values**: `"Fire"`, `"Ice"`, `"Light"`, `"Dark"`, `"Nature"`, `"Physical"`
- **Usage**: Elemental weakness/resistance calculations

##### `canCrit` (boolean)
- **Type**: Boolean
- **Description**: Whether this damage can critically strike
- **Default**: `true` for attacks, `false` for heals

##### `damageType` (string)
- **Type**: Enum
- **Description**: Damage category
- **Values**: `"Physical"`, `"Magical"`, `"True"` (ignores defense)
- **Usage**: Defense/resistance calculations

##### `critBonusDmg` (number)
- **Type**: Float (multiplier)
- **Description**: Additional crit damage multiplier
- **Optional**: Adds to character's base crit damage
- **Example**: `0.5` = +50% crit damage

##### `critRateBonus` (number)
- **Type**: Float (percentage as decimal)
- **Description**: Additional crit chance for this skill
- **Optional**: Adds to character's base crit rate
- **Example**: `0.25` = +25% crit chance

---

### Heal Effect

#### `type: "Heal"`
Restores HP to target(s).

##### `formula` (string)
- **Type**: Healing Calculation Formula
- **Description**: Expression for calculating heal amount
- **Example**: `"(caster.atk * 2.0) + (target.maxHp * 0.15)"`

##### `canCrit` (boolean)
- **Type**: Boolean
- **Description**: Whether healing can crit (usually false)
- **Default**: `false`

---

### Apply Status Effect

#### `type: "ApplyStatus"`
Applies a buff or debuff status effect.

##### `id` (string)
- **Type**: Status Effect ID Reference
- **Description**: ID of status effect to apply
- **Format**: `STATUS_[NAME]`
- **Example**: `"STATUS_BURN"`
- **Links to**: `statusEffects` array in this file

##### `duration` (number)
- **Type**: Integer (turns)
- **Description**: Number of turns the status lasts
- **Range**: 1-10 typical
- **Example**: `3`

##### `chance` (number)
- **Type**: Float (percentage as decimal)
- **Description**: Probability of successful application
- **Range**: 0.0 to 1.0
- **Example**: `0.8` = 80% chance
- **Affected by**: Target's effect resistance

##### `stacks` (number)
- **Type**: Integer
- **Description**: Number of stacks to apply (for stackable statuses)
- **Optional**: Only for stackable status effects
- **Example**: `2`

---

### Conditional Damage Effect

#### `type: "ConditionalDamage"`
Damage that only applies if condition is met.

##### `condition` (string)
- **Type**: Conditional Expression
- **Description**: Boolean condition that must be true
- **Syntax**: JavaScript-like comparison
- **Examples**:
  - `"target.hp < (target.maxHp * 0.35)"` - Execute threshold
  - `"attackFromBehind == true"` - Positional requirement
  - `"target.hasDebuff('BURN')"` - Status check

##### `formula` (string)
- **Type**: Damage Calculation Formula
- **Description**: Damage amount if condition is true
- **Example**: `"target.currentHp * 0.99"` = 99% current HP

##### `description` (string)
- **Type**: Developer Note
- **Description**: Human-readable explanation of condition
- **Usage**: Code documentation, tooltip generation
- **Example**: `"Execute if target HP below 35%"`

---

### Remove Debuffs Effect

#### `type: "RemoveDebuffs"`
Cleanses negative status effects.

##### `count` (number)
- **Type**: Integer
- **Description**: Number of debuffs to remove
- **Example**: `2` = remove 2 debuffs
- **Special**: `-1` or high number for "remove all"

##### `priority` (string)
- **Type**: Enum
- **Description**: Which debuffs to prioritize removing
- **Values**: `"Highest"`, `"Lowest"`, `"Recent"`, `"Oldest"`

---

### Stat Bonus Effect (Passive)

#### `type: "StatBonus"`
Permanent stat increase from passive ability.

##### `stat` (string)
- **Type**: Stat Identifier
- **Description**: Which stat to boost
- **Values**: `"FireDmg"`, `"atk%"`, `"def%"`, `"hp%"`, `"spd"`, etc.

##### `value` (number)
- **Type**: Float
- **Description**: Amount of bonus
- **Format**: Decimal for percentage (0.25 = 25%)
- **Example**: `0.25`

---

### On Event Trigger (Passive)

#### `type: "OnDealDamage"` / `"OnTakeDamage"` / etc.
Reactive passive that triggers on specific events.

##### `condition` (string)
- **Type**: Conditional Expression
- **Description**: Additional requirement for trigger
- **Example**: `"damageElement == Fire"`

##### `chance` (number)
- **Type**: Float (percentage as decimal)
- **Description**: Proc chance when event occurs
- **Example**: `0.3` = 30% chance

##### `effect` (object)
- **Type**: Nested Effect Object
- **Description**: Effect to apply when triggered
- **Contains**: Another effect object (ApplyStatus, Damage, etc.)

---

## Assets Object (Skill)

### `assets` (object)
References to skill's visual and audio assets.

#### `assets.icon` (string)
- **Type**: Addressable Asset Path
- **Description**: Skill icon for UI buttons
- **Format**: `"Addressables/UI/Icons/Skills/[Name]"`

#### `assets.vfx` (string | null)
- **Type**: Addressable Asset Directory Path
- **Description**: Directory containing all skill VFX
- **Format**: `"Addressables/VFX/Skills/[Name]/"`
- **Null**: For passive skills without visuals

---

## Status Effect Object Structure

### `id` (string)
- **Type**: Unique Identifier
- **Description**: Status effect identifier
- **Format**: `STATUS_[NAME]`
- **Example**: `"STATUS_BURN"`

### `meta` (object)
Status metadata.

#### `meta.nameKey` (string)
- **Type**: Localization Key
- **Description**: Display name reference
- **Example**: `"STATUS_NAME_BURN"`

#### `meta.descriptionKey` (string)
- **Type**: Localization Key
- **Description**: Effect description reference
- **Example**: `"STATUS_DESC_BURN"`

#### `meta.icon` (string)
- **Type**: Asset Reference
- **Description**: Status icon sprite
- **Example**: `"Icon_Status_Burn"`

#### `meta.type` (string)
- **Type**: Enum
- **Description**: Status category
- **Values**: `"Buff"`, `"Debuff"`, `"Neutral"`
- **Usage**: Cleanse mechanics, UI coloring

#### `meta.stackable` (boolean)
- **Type**: Boolean
- **Description**: Whether multiple instances can stack
- **Example**: `true`

#### `meta.maxStacks` (number)
- **Type**: Integer
- **Description**: Maximum stack limit
- **Example**: `5`

---

### Status Effect Effects Array

#### `effects` (array)
- **Type**: Array of Status Effect Objects
- **Description**: What the status does each turn/tick

##### Damage Over Time Effect

###### `type: "DamageOverTime"`
- **formula** (string): Damage per tick
- **element** (string): Damage element
- **interval** (number): Seconds between ticks
- **Example**: Burn dealing fire damage each second

##### Heal Over Time Effect

###### `type: "HealOverTime"`
- **formula** (string): Healing per tick
- **interval** (number): Seconds between ticks
- **Example**: Regeneration healing each second

##### Stat Modifier Effect

###### `type: "StatModifier"`
- **stat** (string): Stat to modify
- **modifier** (number): Positive or negative change
- **stackMultiplier** (boolean): Whether each stack applies modifier again
- **Example**: Slow reducing speed by 15% per stack

##### Disable Action Effect

###### `type: "DisableAction"`
- **actions** (array of strings): Actions prevented
- **Values**: `["Move", "Attack", "Skill"]`
- **Example**: Freeze preventing all actions

---

### `vfx` (string)
- **Type**: VFX Prefab Reference
- **Description**: Visual effect displayed on affected unit
- **Example**: `"VFX_Status_Burn"`

---

## Formula Syntax Reference

### Variables
- `caster.atk` - Caster's attack
- `caster.def` - Caster's defense
- `caster.maxHp` - Caster's max HP
- `target.atk` - Target's attack
- `target.def` - Target's defense
- `target.maxHp` - Target's max HP
- `target.currentHp` - Target's current HP

### Operators
- `*` - Multiplication
- `+` - Addition
- `-` - Subtraction
- `/` - Division
- `()` - Grouping
- `pow(base, exp)` - Exponentiation

### Examples
```
"caster.atk * 2.5"                           // Simple scaling
"(caster.atk * 3.0) + (target.maxHp * 0.1)" // Hybrid scaling
"target.currentHp * 0.99"                    // Execute mechanic
"500 + (target.maxHp * 0.15)"               // Flat + percent
```

---

## Targeting Mode Decision Tree

```
Is skill self-only?
├─ Yes → mode: "Self", range: 0.0
└─ No → Does player select target?
    ├─ Yes → Target unit or ground?
    │   ├─ Unit → mode: "TargetUnit"
    │   └─ Ground → mode: "TargetArea"
    └─ No → Automatic selection
        ├─ All allies → mode: "AllAllies"
        ├─ All enemies → mode: "AllEnemies"
        └─ Smart target → mode: "AutoTarget" + filter
```

---

## Skill Type Guidelines

### Basic Attack
- **cooldown**: `0.0`
- **cost**: `[]`
- **tags**: `["Basic"]`
- **Usage**: Spam-able default attack

### Active Skill
- **cooldown**: `5.0` - `10.0`
- **cost**: Moderate mana/energy
- **tags**: Situational tags
- **Usage**: Tactical ability with cooldown

### Ultimate
- **cooldown**: `10.0` - `20.0`
- **cost**: High energy (often 100)
- **tags**: `["Ultimate"]`
- **Usage**: Game-changing powerful ability

### Passive
- **cooldown**: `0.0`
- **cost**: `[]`
- **tags**: `["Passive"]`
- **timeline**: `[{"time": 0.0, "action": "ApplyPassiveBuff"}]`
- **Usage**: Always-active permanent effect

---

## Data Validation Rules

1. **Unique IDs**: All skill and status IDs must be unique
2. **Timeline Ordering**: Timeline events must be sorted by time (ascending)
3. **Formula Syntax**: All formulas must be valid mathematical expressions
4. **Reference Integrity**: Status IDs in ApplyStatus must exist in statusEffects array
5. **Cooldown Logic**: initialCooldown ≤ cooldown
6. **AoE Consistency**: If aoe.enabled is true, shape and radius are required
7. **Target Mode**: Filter is required for AutoTarget mode, null for Self mode

---

## Related Configuration Files

- **Characters.json**: References skills in `skillSet` object
- **GearSets.json**: Set bonuses may reference skill buffs
- **Items.json**: Equipment may modify skill damage/cooldown
- **Localization/**: Contains skill names and descriptions

---

## Version History

- **1.0.0** (2025-11-23): Initial schema documentation
