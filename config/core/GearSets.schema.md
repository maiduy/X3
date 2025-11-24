# GearSets.json Schema Documentation

## Overview
This document provides comprehensive descriptions for all indices (fields) in the `GearSets.json` configuration file. This file defines equipment set bonuses, buff/debuff definitions, and synergy effects when wearing multiple pieces of matching gear.

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

### `sets` (array)
- **Type**: Array of Gear Set Objects
- **Description**: All equipment set bonus configurations
- **Required**: Yes

### `buffs` (array)
- **Type**: Array of Buff Objects
- **Description**: Positive status effects granted by set bonuses
- **Required**: Yes

### `debuffs` (array)
- **Type**: Array of Debuff Objects
- **Description**: Negative status effects applied to enemies by set bonuses
- **Required**: Yes

---

## Gear Set Object Structure

### `id` (string)
- **Type**: Unique Identifier
- **Description**: Immutable set identifier referenced by equipment in Items.json
- **Format**: `SET_[NAME]`
- **Example**: `"SET_INFERNO"`
- **Required**: Yes
- **Constraints**: Must be unique across all sets

---

## Meta Object (Set Metadata)

### `meta` (object)
Contains display and categorization metadata for the gear set.

#### `meta.nameKey` (string)
- **Type**: Localization Key
- **Description**: Reference key for set's display name
- **Format**: `SET_NAME_[SET_ID]`
- **Example**: `"SET_NAME_INFERNO"`
- **Usage**: Displayed in set bonus tooltips

#### `meta.descriptionKey` (string)
- **Type**: Localization Key
- **Description**: Reference key for set's lore/description text
- **Format**: `SET_DESC_[SET_ID]`
- **Example**: `"SET_DESC_INFERNO"`
- **Usage**: Set flavor text, background story

#### `meta.icon` (string)
- **Type**: Asset Reference
- **Description**: Icon representing the set theme
- **Example**: `"Icon_Set_Inferno"`
- **Usage**: Set bonus UI, collection displays

#### `meta.element` (string)
- **Type**: Element Enum
- **Description**: Thematic element associated with this set
- **Values**: `"Fire"`, `"Ice"`, `"Light"`, `"Dark"`, `"Nature"`, `"Wind"`, `"Physical"`
- **Usage**: Visual theming, elemental synergies

#### `meta.tier` (string)
- **Type**: Rarity Enum
- **Description**: Set rarity/power level
- **Values**: `"R"`, `"SR"`, `"SSR"`
- **Usage**: Determines set bonus strength, acquisition difficulty
- **Impact**: SSR sets have stronger bonuses than SR/R

---

## Pieces Object

### `pieces` (object)
Defines bonuses granted for wearing multiple pieces of the set.

**Structure**: Keys are piece counts (`"2"`, `"4"`, `"6"`), values are bonus configurations.

---

### 2-Piece Bonus

#### `pieces["2"]` (object)
Bonus active when wearing 2 pieces of this set.

##### `name` (string)
- **Type**: Bonus Name
- **Description**: Short memorable name for this bonus tier
- **Example**: `"Blazing Power"`
- **Usage**: UI displays, tooltips

##### `description` (string)
- **Type**: Player-Facing Text
- **Description**: Clear explanation of bonus effect
- **Example**: `"Increases Fire DMG by 15%"`
- **Best Practice**: Include exact numbers, avoid vague terms

##### `effects` (array)
- **Type**: Array of Effect Objects
- **Description**: Mechanical implementation of the bonus
- **Required**: Yes, must have at least one effect

---

### 4-Piece Bonus

#### `pieces["4"]` (object)
Bonus active when wearing 4 pieces of this set (stacks with 2-piece).

**Structure**: Same as 2-piece bonus (name, description, effects)

**Important**: 4-piece bonuses are in addition to 2-piece, not replacing them.

---

### 6-Piece Bonus (Optional)

#### `pieces["6"]` (object)
Optional bonus for sets with 6+ possible pieces (rare).

**Structure**: Same as 2-piece and 4-piece bonuses

**Usage**: For extensive sets covering all equipment slots

---

## Effect Object Types

### Stat Bonus Effect

#### `type: "StatBonus"`
Permanent increase to a character stat.

##### `stat` (string)
- **Type**: Stat Identifier
- **Description**: Which stat to increase
- **Common Values**:
  - Flat: `"hp"`, `"atk"`, `"def"`, `"spd"`
  - Percentage: `"hp%"`, `"atk%"`, `"def%"`, `"spd%"`
  - Special: `"critRate"`, `"critDmg"`, `"effectRes"`, `"effectHit"`

##### `value` (number)
- **Type**: Float
- **Description**: Amount of bonus
- **Format**:
  - Flat stats: Integer (e.g., `50` = +50 HP)
  - Percentage: Decimal (e.g., `0.20` = +20%)
- **Example**: `0.20` for DEF +20%

---

### Elemental Damage Bonus Effect

#### `type: "ElementalDmgBonus"`
Increases damage of specific element(s).

##### `element` (string)
- **Type**: Element Identifier
- **Description**: Which element to boost
- **Values**: `"Fire"`, `"Ice"`, `"Light"`, `"Dark"`, `"Nature"`, `"Wind"`, `"Physical"`, `"All"`
- **Example**: `"Fire"`

##### `value` (number)
- **Type**: Float (percentage as decimal)
- **Description**: Damage increase multiplier
- **Format**: `0.15` = +15% damage
- **Example**: `0.15`

---

### On Skill Use Trigger

#### `type: "OnSkillUse"`
Effect that triggers when character uses a skill.

##### `trigger` (string)
- **Type**: Trigger Timing Enum
- **Description**: When during skill usage to trigger
- **Values**:
  - `"BeforeSkillCast"` - Before skill executes
  - `"AfterSkillCast"` - After skill completes
  - `"OnSkillHit"` - When skill damages enemy

##### `buff` (object | string)
- **Type**: Buff Configuration or Buff ID
- **Description**: Buff to apply when triggered
- **Options**:
  1. **String**: Reference to buff in `buffs` array
     - Example: `"BUFF_INFERNO_ATK"`
  2. **Object**: Inline buff definition

##### Inline Buff Object Fields:

###### `id` (string)
- **Type**: Buff Identifier
- **Description**: Unique ID for this buff instance
- **Example**: `"BUFF_INFERNO_ATK"`

###### `stat` (string)
- **Type**: Stat Identifier
- **Description**: Stat affected by buff
- **Example**: `"atk%"`

###### `value` (number)
- **Type**: Float
- **Description**: Buff strength
- **Example**: `0.25` = +25% ATK

###### `duration` (number)
- **Type**: Integer (turns)
- **Description**: How many turns buff lasts
- **Example**: `3`

###### `maxStacks` (number)
- **Type**: Integer
- **Description**: Maximum number of times buff can stack
- **Example**: `3`

###### `stackBehavior` (string)
- **Type**: Stacking Mode Enum
- **Description**: How multiple stacks interact
- **Values**:
  - `"Independent"` - Each stack has its own duration
  - `"Refresh"` - New application refreshes all stacks
  - `"Extend"` - New application extends duration

---

### On Hit Trigger

#### `type: "OnHit"`
Effect that triggers when character is hit by attacks.

##### `trigger` (string)
- **Type**: Trigger Timing Enum
- **Description**: When to trigger relative to hit
- **Values**:
  - `"WhenReceiveDamage"` - After taking damage
  - `"BeforeReceiveDamage"` - Before damage calculation
  - `"OnDodge"` - When attack is dodged

##### `chance` (number)
- **Type**: Float (percentage as decimal)
- **Description**: Probability of effect triggering
- **Format**: `0.5` = 50% chance
- **Range**: 0.0 to 1.0

##### `cooldown` (number)
- **Type**: Integer (turns)
- **Description**: Turns before effect can trigger again
- **Optional**: Omit for no cooldown
- **Example**: `3` = 3-turn cooldown

##### `effect` (object)
- **Type**: Nested Effect Object
- **Description**: Effect to apply when triggered

##### Common Nested Effects:

###### Apply Debuff Effect

- **type** (string): `"ApplyDebuff"`
- **id** (string): Debuff ID reference
- **duration** (number): Turns debuff lasts
- **target** (string): `"Attacker"`, `"Self"`, `"AllEnemies"`
- **Example**: `{"type": "ApplyDebuff", "id": "DEBUFF_FREEZE", "duration": 1, "target": "Attacker"}`

###### Counterattack Effect

- **type** (string): `"Counterattack"`
- **damageMultiplier** (number): Damage as multiplier of ATK (e.g., `0.75` = 75% ATK)
- **critRateBonus** (number): Bonus crit chance (e.g., `0.30` = +30%)
- **target** (string): `"Attacker"`
- **Example**: `{"type": "Counterattack", "damageMultiplier": 0.75, "target": "Attacker"}`

---

### Lifesteal Effect

#### `type: "Lifesteal"`
Heals character for percentage of damage dealt.

##### `value` (number)
- **Type**: Float (percentage as decimal)
- **Description**: Percentage of damage converted to healing
- **Format**: `0.10` = 10% lifesteal
- **Example**: `0.10`

---

### Conditional Buff Effect

#### `type: "ConditionalBuff"`
Buff that only applies when specific condition is met.

##### `condition` (string)
- **Type**: Condition Enum
- **Description**: When buff is active
- **Common Values**:
  - `"HP_BELOW_50"` - When HP < 50%
  - `"HP_ABOVE_80"` - When HP > 80%
  - `"FULL_HP"` - When HP = 100%
  - `"IN_COMBAT"` - During battle
  - `"HAS_BUFF"` - When specific buff active
  - `"HAS_DEBUFF"` - When specific debuff active

##### `effects` (array)
- **Type**: Array of Effect Objects
- **Description**: Effects active while condition is true
- **Can Include**: StatBonus, Lifesteal, ElementalDmgBonus, etc.

---

### On Battle Start Effect

#### `type: "OnBattleStart"`
Effect that triggers once at the beginning of combat.

##### `effect` (object)
- **Type**: Nested Effect Object
- **Description**: Effect to apply at battle start

##### Common Nested Effects:

###### Apply Buff

- **type** (string): `"ApplyBuff"`
- **id** (string): Buff ID reference
- **duration** (number): Turns buff lasts
- **target** (string): `"Self"`, `"AllAllies"`
- **Example**: `{"type": "ApplyBuff", "id": "BUFF_IMMUNITY", "duration": 2, "target": "Self"}`

###### Increase ATB (Action Turn Bar)

- **type** (string): `"IncreaseATB"`
- **value** (number): ATB increase amount (e.g., `0.15` = +15% ATB)
- **target** (string): `"Self"`, `"AllAllies"`
- **Example**: `{"type": "IncreaseATB", "value": 0.15, "target": "Self"}`

---

## Required Slots Array

### `requiredSlots` (array)
- **Type**: Array of Equipment Slot Identifiers
- **Description**: Which equipment slots count towards this set
- **Values**: `"Weapon"`, `"Helmet"`, `"Chest"`, `"Gloves"`, `"Boots"`, `"Ring"`, `"Necklace"`, `"Earring"`
- **Length**: Typically 4 slots for 4-piece sets
- **Example**: `["Weapon", "Chest", "Gloves", "Boots"]`
- **Usage**: Determines which items can contribute to set bonuses

**Important**: Only items in these slots with matching `setId` count toward set bonuses.

---

## Buff Object Structure

### `id` (string)
- **Type**: Unique Identifier
- **Description**: Buff identifier referenced by set effects
- **Format**: `BUFF_[NAME]`
- **Example**: `"BUFF_INFERNO_ATK"`

### `name` (string)
- **Type**: Display Name
- **Description**: Player-visible buff name
- **Example**: `"Inferno's Power"`

### `type` (string)
- **Type**: Effect Type
- **Description**: Classification of effect
- **Value**: `"Buff"` for positive effects

### `icon` (string)
- **Type**: Asset Reference
- **Description**: Buff icon displayed in UI
- **Example**: `"Icon_Buff_InfernoATK"`

### `effects` (array)
- **Type**: Array of Effect Objects
- **Description**: Mechanical effects of the buff
- **Common Types**:
  - `StatModifier` - Changes character stat
  - `DebuffImmunity` - Prevents debuff application
  - `DamageBonus` - Increases damage output

#### Stat Modifier Effect

##### `type: "StatModifier"`
- **stat** (string): Stat to modify
- **value** (number): Modification amount
- **Example**: `{"type": "StatModifier", "stat": "atk%", "value": 0.25}`

#### Debuff Immunity Effect

##### `type: "DebuffImmunity"`
- **value** (number): Immunity strength (1.0 = full immunity)
- **Example**: `{"type": "DebuffImmunity", "value": 1.0}`

### `visual` (object)
- **Type**: Visual Effect Configuration
- **Description**: Display properties for buff

#### `vfx` (string)
- **Type**: VFX Asset Reference
- **Description**: Particle effect shown on buffed character
- **Example**: `"VFX_Buff_InfernoATK"`

#### `color` (string)
- **Type**: Hex Color Code
- **Description**: UI color theme for buff
- **Format**: `"#RRGGBB"`
- **Example**: `"#FF4500"` (orange-red for fire)

---

## Debuff Object Structure

### `id` (string)
- **Type**: Unique Identifier
- **Description**: Debuff identifier referenced by set effects
- **Format**: `DEBUFF_[NAME]`
- **Example**: `"DEBUFF_FREEZE"`

### `name` (string)
- **Type**: Display Name
- **Description**: Player-visible debuff name
- **Example**: `"Frozen"`

### `type` (string)
- **Type**: Effect Type
- **Description**: Classification of effect
- **Value**: `"Debuff"` for negative effects

### `icon` (string)
- **Type**: Asset Reference
- **Description**: Debuff icon displayed in UI
- **Example**: `"Icon_Debuff_Freeze"`

### `effects` (array)
- **Type**: Array of Effect Objects
- **Description**: Mechanical penalties of the debuff

#### Disable Action Effect

##### `type: "DisableAction"`
Prevents character from performing specific actions.

- **actions** (array of strings): Actions to disable
- **Values**: `["Move", "Attack", "Skill", "Ultimate", "Item"]`
- **Example**: `{"type": "DisableAction", "actions": ["Move", "Attack", "Skill"]}`
- **Usage**: Stun, freeze, silence effects

### `visual` (object)
Same structure as buff visual (vfx, color).

---

## Set Design Guidelines

### 2-Piece Bonus Design
- **Purpose**: Passive stat boost or simple effect
- **Power Level**: Moderate, always useful
- **Examples**:
  - Flat stat increase (+20% DEF)
  - Elemental damage bonus (+15% Fire DMG)
  - Lifesteal (10% lifesteal)

### 4-Piece Bonus Design
- **Purpose**: Unique mechanic or powerful conditional effect
- **Power Level**: High, build-defining
- **Examples**:
  - Proc-based effects (counterattack, freeze on hit)
  - Stacking buffs (ATK increase on skill use)
  - Battle start advantages (immunity, ATB boost)

### Tier Balancing
- **SSR Sets**: Strongest bonuses, build-defining effects
- **SR Sets**: Solid bonuses, useful for mid-game
- **R Sets**: Basic bonuses, entry-level gear

---

## Set Synergy Examples

### Damage Dealer Build (SET_INFERNO)
```
2-piece: Fire DMG +15%
4-piece: ATK +25% for 3 turns after skill (stacks 3x)
Result: High burst damage with skill rotation
```

### Tank Build (SET_GLACIER)
```
2-piece: DEF +20%
4-piece: 50% chance to freeze attacker for 1 turn
Result: Defensive tanking with CC utility
```

### Sustain DPS Build (SET_LIFESTEAL)
```
2-piece: Lifesteal 10%
4-piece: When HP < 50%, lifesteal becomes 25% and ATK +30%
Result: Self-sustaining damage dealer
```

---

## Data Validation Rules

1. **Unique IDs**: All set, buff, and debuff IDs must be unique
2. **Reference Integrity**: Buff/debuff IDs in effects must exist in their respective arrays
3. **Piece Progression**: Sets should have 2-piece bonuses before 4-piece bonuses
4. **Required Slots Count**: Must have enough slots for highest piece count (4 slots for 4-piece set)
5. **Stat Value Ranges**: Stat bonuses should be balanced (typically 10-30% for 2-piece, higher for 4-piece)
6. **Trigger Chances**: All chance values must be between 0.0 and 1.0
7. **Duration Limits**: Buff/debuff durations typically 1-5 turns

---

## Common Use Cases

### Finding Best Set for Character
1. Check character's element and role (from Characters.json)
2. Filter sets by matching `meta.element`
3. Compare 2-piece and 4-piece bonuses
4. Verify `requiredSlots` match available equipment

### Set Bonus Calculation
1. Count equipped items with matching `setId`
2. Check if count ≥ 2 for 2-piece bonus
3. Check if count ≥ 4 for 4-piece bonus
4. Apply all active bonuses cumulatively

### Team Composition Planning
1. Review each character's equipped sets
2. Identify synergies (e.g., Speed set for support, Damage sets for DPS)
3. Ensure tank has defensive sets (Glacier, Immunity)
4. Balance offensive and defensive set bonuses

---

## Related Configuration Files

- **Items.json**: Equipment items reference sets via `setBonus.setId`
- **Characters.json**: Character stats modified by set bonuses
- **Skills.json**: Set bonuses can trigger on skill use or modify skill effects
- **BattleRules.json**: Set bonus effects interact with combat mechanics
- **Localization/**: Set names, descriptions, and bonus text

---

## Version History

- **1.0.0** (2025-11-23): Initial schema documentation
