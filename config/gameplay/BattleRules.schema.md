# BattleRules.json Schema Documentation

## Overview
This document provides comprehensive descriptions for all indices (fields) in the `BattleRules.json` configuration file. This file defines the core combat mechanics, turn systems, damage calculations, elemental interactions, victory/defeat conditions, and reward formulas for the game's battle system.

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

---

## Turn System Object

### `turnSystem` (object)
Defines the turn-based combat system mechanics.

#### `type` (string)
- **Type**: Turn System Type Enum
- **Description**: Combat pacing mechanism
- **Values**:
  - `"ATB"` - Action Time Battle (active time battle)
  - `"TurnBased"` - Traditional turn-based
  - `"RealTime"` - Continuous real-time combat
- **Current**: `"ATB"`
- **Usage**: Determines turn order calculation method

#### `baseTickRate` (number)
- **Type**: Integer
- **Description**: Base rate at which ATB bars fill per game tick
- **Default**: `100`
- **Usage**: ATB system timing baseline
- **Note**: Higher values = faster combat pace

#### `speedToATBMultiplier` (number)
- **Type**: Float (multiplier)
- **Description**: Conversion rate from character speed stat to ATB fill speed
- **Default**: `1.0`
- **Formula**: `ATB_Fill_Speed = character.spd * speedToATBMultiplier`
- **Example**: SPD 100 with 1.0 multiplier = 100 ATB units/tick

#### `actionCost` (number)
- **Type**: Integer
- **Description**: ATB cost to take an action (depletes ATB bar)
- **Default**: `100`
- **Usage**: After character acts, their ATB is reduced by this amount
- **Result**: Characters with higher SPD act more frequently

#### `turnOrderDisplay` (boolean)
- **Type**: Boolean
- **Description**: Whether to show turn order UI to player
- **Default**: `true`
- **Usage**: Player can see upcoming turn sequence
- **Benefits**: Enables strategic planning

---

## Combat Phases Object

### `combatPhases` (object)
Defines different stages of a battle encounter.

---

### Preparation Phase

#### `preparation` (object)
Pre-battle setup phase.

##### `duration` (number)
- **Type**: Float (seconds)
- **Description**: Time allowed for preparation phase
- **Default**: `3.0`
- **Usage**: Player reviews team, selects skills, checks enemy info

##### `allowSkillSelection` (boolean)
- **Type**: Boolean
- **Description**: Whether players can change skill loadout during prep
- **Default**: `true`

##### `allowPositioning` (boolean)
- **Type**: Boolean
- **Description**: Whether players can change unit positions
- **Default**: `false`
- **Note**: Set to `true` for tactical positioning games

---

### Combat Phase

#### `combat` (object)
Active battle phase.

##### `maxTurns` (number)
- **Type**: Integer
- **Description**: Maximum number of turns before forced draw/defeat
- **Default**: `50`
- **Usage**: Prevents infinite battles, encourages aggressive play
- **Range**: Typically 30-100

##### `turnTimeout` (number)
- **Type**: Float (seconds)
- **Description**: Time limit per player turn for manual play
- **Default**: `30.0`
- **Usage**: Prevents AFK/stalling
- **Behavior**: Auto-play if player doesn't act in time

##### `autoPlayEnabled` (boolean)
- **Type**: Boolean
- **Description**: Whether AI can control player units
- **Default**: `true`
- **Usage**: Auto-battle feature, timeout fallback

---

### Victory Phase

#### `victory` (object)
Post-win celebration phase.

##### `duration` (number)
- **Type**: Float (seconds)
- **Description**: Length of victory screen display
- **Default**: `2.0`
- **Usage**: Show victory animation, celebration effects

##### `showRewards` (boolean)
- **Type**: Boolean
- **Description**: Whether to display rewards during victory phase
- **Default**: `true`
- **Usage**: Immediate reward feedback

---

### Defeat Phase

#### `defeat` (object)
Post-loss phase.

##### `duration` (number)
- **Type**: Float (seconds)
- **Description**: Length of defeat screen display
- **Default**: `2.0`

##### `allowRetry` (boolean)
- **Type**: Boolean
- **Description**: Whether player can retry battle immediately
- **Default**: `true`
- **Usage**: Quick retry button availability

---

## Elemental System Object

### `elementalSystem` (object)
Defines elemental damage types and their interactions.

#### `elements` (array)
- **Type**: Array of Element Identifiers
- **Description**: All available elemental damage types in the game
- **Values**: `["Fire", "Ice", "Nature", "Light", "Dark", "Physical"]`
- **Usage**: Skill element assignment, character affinity

#### `advantageMatrix` (object)
- **Type**: Element Interaction Map
- **Description**: Defines which elements are strong/weak against others
- **Structure**: `{ElementName: {strong: [array], weak: [array]}}`

##### Matrix Entry Fields:

###### `strong` (array)
- **Type**: Array of Element Names
- **Description**: Elements this element deals bonus damage to
- **Example**: Fire is strong against Nature and Ice
- **Multiplier**: Applied from `advantageMultiplier.strong`

###### `weak` (array)
- **Type**: Array of Element Names
- **Description**: Elements this element deals reduced damage to
- **Example**: Fire is weak against Ice and Water
- **Multiplier**: Applied from `advantageMultiplier.weak`

**Example Matrix Entry**:
```json
"Fire": {
  "strong": ["Nature", "Ice"],
  "weak": ["Ice", "Water"]
}
```

#### `advantageMultiplier` (object)
Damage multipliers based on elemental matchup.

##### `strong` (number)
- **Type**: Float (multiplier)
- **Description**: Damage multiplier when attacking weak element
- **Default**: `1.5` (150% damage)
- **Example**: Fire attack vs Nature enemy = 1.5x damage

##### `neutral` (number)
- **Type**: Float (multiplier)
- **Description**: Damage multiplier when no advantage/disadvantage
- **Default**: `1.0` (100% damage)

##### `weak` (number)
- **Type**: Float (multiplier)
- **Description**: Damage multiplier when attacking resistant element
- **Default**: `0.75` (75% damage)
- **Example**: Fire attack vs Water enemy = 0.75x damage

---

## Damage Calculation Object

### `damageCalculation` (object)
Formulas and parameters for damage computation.

#### `physicalFormula` (string)
- **Type**: Damage Calculation Formula
- **Description**: Formula for calculating physical damage
- **Formula**: `"(atk * skillMultiplier * (1 + critDmg * isCrit)) * (100 / (100 + def)) * elementalMultiplier * randomFactor"`
- **Variables**:
  - `atk` - Attacker's attack stat
  - `skillMultiplier` - Skill's damage multiplier
  - `critDmg` - Critical damage bonus
  - `isCrit` - 1 if crit, 0 if not (binary)
  - `def` - Target's defense stat
  - `elementalMultiplier` - From elemental advantage system
  - `randomFactor` - Random variance (0.95-1.05)

**Calculation Steps**:
1. Base damage: `atk * skillMultiplier`
2. Critical damage: `baseDamage * (1 + critDmg * isCrit)`
3. Defense reduction: `damage * (100 / (100 + def))`
4. Elemental modifier: `damage * elementalMultiplier`
5. Random variance: `damage * randomFactor`

#### `magicalFormula` (string)
- **Type**: Damage Calculation Formula
- **Description**: Formula for calculating magical damage
- **Formula**: `"(atk * skillMultiplier * (1 + critDmg * isCrit)) * (100 / (100 + def * 0.5)) * elementalMultiplier * randomFactor"`
- **Difference from Physical**: Defense is only 50% effective (`def * 0.5`)
- **Rationale**: Magic penetrates armor better than physical attacks

#### `trueDamageFormula` (string)
- **Type**: Damage Calculation Formula
- **Description**: Formula for true damage (ignores defense)
- **Formula**: `"atk * skillMultiplier"`
- **Usage**: Execute abilities, pure damage skills
- **Note**: No defense, no elemental, no random factor

#### `randomFactor` (object)
Variance applied to final damage.

##### `min` (number)
- **Type**: Float
- **Description**: Minimum damage variance multiplier
- **Default**: `0.95` (95% damage)

##### `max` (number)
- **Type**: Float
- **Description**: Maximum damage variance multiplier
- **Default**: `1.05` (105% damage)

**Purpose**: Adds unpredictability, prevents exact damage prediction

#### `criticalHit` (object)
Critical strike system parameters.

##### `baseCritRate` (number)
- **Type**: Float (percentage as decimal)
- **Description**: Default crit chance before modifiers
- **Default**: `0.05` (5%)
- **Usage**: Baseline for all characters

##### `baseCritDmg` (number)
- **Type**: Float (multiplier)
- **Description**: Default crit damage multiplier before bonuses
- **Default**: `1.5` (150% damage = +50% bonus)
- **Formula**: `damage * (1 + baseCritDmg)`

##### `critRateCap` (number)
- **Type**: Float (percentage as decimal)
- **Description**: Maximum achievable critical hit chance
- **Default**: `1.0` (100%)
- **Usage**: Prevents guaranteed crits (set to 0.99 if desired)

##### `critDmgCap` (number)
- **Type**: Float (multiplier)
- **Description**: Maximum achievable critical damage multiplier
- **Default**: `3.0` (300% damage = +200% bonus)
- **Usage**: Balance high-crit builds

#### `defenseFormulas` (object)
Defense-related calculations.

##### `damageReduction` (string)
- **Type**: Damage Reduction Formula
- **Description**: Percentage of damage reduced by defense
- **Formula**: `"def / (def + 100)"`
- **Example**: 100 DEF = 100/(100+100) = 50% reduction

##### `defenseIgnore` (string)
- **Type**: Defense Penetration Formula
- **Description**: Effective defense after penetration
- **Formula**: `"max(0, def - (def * ignoreValue))"`
- **Example**: 40% ignore on 100 DEF = 100 - 40 = 60 effective DEF

---

## Resource System Object

### `resourceSystem` (object)
Defines mana, energy, and other combat resources.

---

### Mana Resource

#### `mana` (object)

##### `startAmount` (number)
- **Type**: Integer
- **Description**: Starting mana at battle start
- **Default**: `100`

##### `maxAmount` (number)
- **Type**: Integer
- **Description**: Maximum mana capacity
- **Default**: `100`

##### `regenPerTurn` (number)
- **Type**: Integer
- **Description**: Mana restored at end of each turn
- **Default**: `20`
- **Usage**: 5 turns to full from empty

##### `regenOnHit` (number)
- **Type**: Integer
- **Description**: Mana gained when dealing damage
- **Default**: `5`
- **Usage**: Rewards aggressive play

##### `regenOnKill` (number)
- **Type**: Integer
- **Description**: Mana gained when killing enemy
- **Default**: `30`
- **Usage**: Momentum mechanic, enables combos

---

### Energy Resource

#### `energy` (object)

##### `startAmount` (number)
- **Type**: Integer
- **Description**: Starting energy at battle start
- **Default**: `0`
- **Usage**: Must build energy to use ultimates

##### `maxAmount` (number)
- **Type**: Integer
- **Description**: Maximum energy capacity
- **Default**: `100`

##### `gainOnAttack` (number)
- **Type**: Integer
- **Description**: Energy gained when attacking
- **Default**: `10`
- **Usage**: 10 attacks to full ultimate

##### `gainOnHit` (number)
- **Type**: Integer
- **Description**: Energy gained when taking damage
- **Default**: `5`
- **Usage**: Being hit accelerates ultimate availability

##### `gainOnKill` (number)
- **Type**: Integer
- **Description**: Energy gained when killing enemy
- **Default**: `20`
- **Usage**: Rewards eliminations

##### `decayPerTurn` (number)
- **Type**: Integer
- **Description**: Energy lost at end of turn
- **Default**: `0`
- **Usage**: Set to >0 for "use it or lose it" mechanic

---

## Combat Rules Object

### `combatRules` (object)
General combat behavior rules.

#### `allowFriendlyFire` (boolean)
- **Type**: Boolean
- **Description**: Whether AoE skills can damage allies
- **Default**: `false`
- **Usage**: Set to `true` for tactical challenge

#### `allowSelfTarget` (boolean)
- **Type**: Boolean
- **Description**: Whether characters can target themselves
- **Default**: `true`
- **Usage**: Enables self-buffs and heals

#### `resurrectAllowed` (boolean)
- **Type**: Boolean
- **Description**: Whether characters can be revived mid-battle
- **Default**: `true`
- **Usage**: Resurrection skills functionality

#### `maxRevivesPerBattle` (number)
- **Type**: Integer
- **Description**: Maximum times a single character can be revived
- **Default**: `3`
- **Usage**: Prevents infinite resurrection loops

#### `speedTiebreakerRandom` (boolean)
- **Type**: Boolean
- **Description**: How to handle same-speed characters
- **Default**: `true`
- **Values**:
  - `true` - Random turn order for tied speeds
  - `false` - Deterministic (e.g., player first)

#### `continuousEffectTiming` (string)
- **Type**: Effect Timing Enum
- **Description**: When DoTs/HoTs/buffs tick
- **Values**:
  - `"StartOfTurn"` - Tick before character acts
  - `"EndOfTurn"` - Tick after character acts
  - `"ImmediateApplication"` - Tick immediately when applied
- **Default**: `"EndOfTurn"`

---

## Crowd Control Rules Object

### `crowdControlRules` (object)
Rules for CC (crowd control) effects.

#### `stunPreventsAction` (boolean)
- **Type**: Boolean
- **Description**: Whether stunned units skip their turn
- **Default**: `true`

#### `freezePreventsAction` (boolean)
- **Type**: Boolean
- **Description**: Whether frozen units skip their turn
- **Default**: `true`

#### `sleepBreaksOnDamage` (boolean)
- **Type**: Boolean
- **Description**: Whether sleep ends when unit takes damage
- **Default**: `true`
- **Usage**: Sleep is fragile CC

#### `charmChangesTeam` (boolean)
- **Type**: Boolean
- **Description**: Whether charmed units attack allies
- **Default**: `true`
- **Usage**: Mind control mechanic

#### `maxCCDuration` (number)
- **Type**: Integer (turns)
- **Description**: Maximum duration any CC can last
- **Default**: `5`
- **Usage**: Prevents permanent CC lock

#### `ccResistanceStacking` (string)
- **Type**: Stacking Mode Enum
- **Description**: How multiple CC resist sources combine
- **Values**:
  - `"Additive"` - Resistances add together
  - `"Multiplicative"` - Resistances multiply together
- **Default**: `"Multiplicative"`
- **Example Multiplicative**: 20% + 20% = 36% total (not 40%)
- **Formula**: `1 - ((1 - 0.20) * (1 - 0.20)) = 0.36`

---

## Team Composition Object

### `teamComposition` (object)
Rules for team building.

---

### PvP Teams

#### `pvp` (object)

##### `minUnits` (number)
- **Type**: Integer
- **Description**: Minimum team size for PvP
- **Default**: `3`

##### `maxUnits` (number)
- **Type**: Integer
- **Description**: Maximum team size for PvP
- **Default**: `5`

##### `duplicatesAllowed` (boolean)
- **Type**: Boolean
- **Description**: Whether same character can appear multiple times
- **Default**: `false`

##### `sameElementLimit` (number | null)
- **Type**: Integer or Null
- **Description**: Maximum characters of same element
- **Default**: `null` (no limit)
- **Example**: `3` = max 3 fire units

---

### PvE Teams

#### `pve` (object)

##### `minUnits` (number)
- **Type**: Integer
- **Description**: Minimum team size for PvE
- **Default**: `1`
- **Usage**: Solo challenges possible

##### `maxUnits` (number)
- **Type**: Integer
- **Description**: Maximum team size for PvE
- **Default**: `5`

##### `duplicatesAllowed` (boolean)
- **Type**: Boolean
- **Description**: Whether same character can appear multiple times
- **Default**: `false`

##### `sameElementLimit` (number | null)
- **Type**: Integer or Null
- **Description**: Maximum characters of same element
- **Default**: `null` (no limit)

---

## Victory Conditions Array

### `victoryConditions` (array)
- **Type**: Array of Victory Condition Objects
- **Description**: Ways to win a battle

### Victory Condition Object

#### `id` (string)
- **Type**: Unique Identifier
- **Description**: Victory condition ID
- **Format**: `"VICTORY_TYPE"`
- **Examples**: `"ELIMINATE_ALL"`, `"SURVIVE_TURNS"`, `"PROTECT_TARGET"`

#### `type` (string)
- **Type**: Victory Type Enum
- **Description**: Victory condition mechanic
- **Values**:
  - `"EliminateEnemies"` - Kill all enemies
  - `"SurviveDuration"` - Survive N turns
  - `"KeepAlive"` - Protect specific unit
  - `"EliminateSpecific"` - Kill specific enemy (boss)
  - `"CollectItems"` - Gather objectives
  - `"ReachLocation"` - Move to goal

#### `description` (string)
- **Type**: Display Text
- **Description**: Player-facing victory condition text
- **Example**: `"Defeat all enemy units"`

#### `param` (object | null)
- **Type**: Parameter Object
- **Description**: Additional parameters for condition
- **Optional**: Not needed for simple conditions
- **Examples**:
  - `{"turns": 10}` - For SurviveDuration
  - `{"targetId": "PROTECTED_UNIT"}` - For KeepAlive
  - `{"targetTag": "Boss"}` - For EliminateSpecific

---

## Defeat Conditions Array

### `defeatConditions` (array)
- **Type**: Array of Defeat Condition Objects
- **Description**: Ways to lose a battle

### Defeat Condition Object

#### `id` (string)
- **Type**: Unique Identifier
- **Description**: Defeat condition ID
- **Format**: `"DEFEAT_TYPE"`
- **Examples**: `"ALL_DEAD"`, `"TIMEOUT"`, `"VIP_DEAD"`

#### `type` (string)
- **Type**: Defeat Type Enum
- **Description**: Defeat condition mechanic
- **Values**:
  - `"AllUnitsDefeated"` - All player units dead
  - `"TurnLimit"` - Exceeded max turns
  - `"ProtectedUnitDied"` - VIP died
  - `"FailedObjective"` - Missed objective
  - `"Retreated"` - Player fled battle

#### `description` (string)
- **Type**: Display Text
- **Description**: Player-facing defeat condition text
- **Example**: `"All player units are defeated"`

#### `param` (object | null)
- **Type**: Parameter Object
- **Description**: Additional parameters for condition
- **Examples**:
  - `{"maxTurns": 50}` - For TurnLimit
  - `{"targetId": "VIP_UNIT"}` - For ProtectedUnitDied

---

## Reward Calculation Object

### `rewardCalculation` (object)
Formulas for calculating battle rewards.

#### `baseExpFormula` (string)
- **Type**: Reward Formula
- **Description**: Base experience points calculation
- **Formula**: `"enemyLevel * 10 + enemyRarity * 50"`
- **Variables**:
  - `enemyLevel` - Level of defeated enemies
  - `enemyRarity` - Rarity tier (R=1, SR=2, SSR=3)
- **Example**: Level 20 SR enemy = 20*10 + 2*50 = 300 EXP

#### `baseGoldFormula` (string)
- **Type**: Reward Formula
- **Description**: Base gold reward calculation
- **Formula**: `"enemyLevel * 5 + enemyRarity * 25"`
- **Example**: Level 20 SR enemy = 20*5 + 2*25 = 150 Gold

#### `performanceMultipliers` (object)
Bonus multipliers for exceptional performance.

##### `noDeaths` (number)
- **Type**: Float (multiplier)
- **Description**: Bonus for clearing without losing units
- **Default**: `1.5` (150% rewards = +50% bonus)

##### `fastClear` (number)
- **Type**: Float (multiplier)
- **Description**: Bonus for clearing quickly (e.g., <10 turns)
- **Default**: `1.3` (130% rewards = +30% bonus)

##### `perfectHealth` (number)
- **Type**: Float (multiplier)
- **Description**: Bonus for clearing with all units at full HP
- **Default**: `1.2` (120% rewards = +20% bonus)

**Stacking**: Multipliers typically multiply together
```
Final Reward = baseReward * noDeaths * fastClear * perfectHealth
Example: 100 base * 1.5 * 1.3 * 1.2 = 234 total reward
```

#### `firstClearBonus` (number)
- **Type**: Float (multiplier)
- **Description**: One-time bonus for first clear of stage
- **Default**: `2.0` (200% rewards = double)
- **Usage**: Encourages exploration, progression

---

## ATB System Calculation Example

### Character Turn Order

**Given**:
- Character A: SPD 120
- Character B: SPD 100
- Character C: SPD 80

**ATB Calculation**:
```
Fill Rate = SPD * speedToATBMultiplier (1.0)
Action Threshold = actionCost (100)

Character A: Fills at 120 units/tick → Acts every 100/120 = 0.833 ticks
Character B: Fills at 100 units/tick → Acts every 100/100 = 1.0 tick
Character C: Fills at 80 units/tick → Acts every 100/80 = 1.25 ticks

Turn Order: A → B → A → C → A → B → A → ...
```

---

## Damage Calculation Example

### Physical Attack Scenario

**Given**:
- Attacker: ATK 500, Crit Rate 20%, Crit Dmg 150%
- Defender: DEF 200, Element Nature (weak to Fire)
- Skill: Fire element, 2.5x multiplier
- Roll: Crits, randomFactor = 1.02

**Calculation**:
```
1. Base: 500 * 2.5 = 1250
2. Crit: 1250 * (1 + 1.5) = 1250 * 2.5 = 3125
3. Defense: 3125 * (100 / (100 + 200)) = 3125 * 0.333 = 1041
4. Elemental: 1041 * 1.5 (Fire vs Nature) = 1562
5. Random: 1562 * 1.02 = 1593 final damage
```

---

## Data Validation Rules

1. **Turn System**: actionCost should match ATB scale (typically 100)
2. **Multipliers**: All advantage/performance multipliers should be ≥ 0
3. **Caps**: Crit rate cap should be ≤ 1.0, crit dmg cap reasonable (2.0-5.0)
4. **Team Limits**: maxUnits ≥ minUnits
5. **CC Duration**: maxCCDuration should be reasonable (3-10 turns)
6. **Victory/Defeat IDs**: Must be unique
7. **Formula Syntax**: All formulas must be valid mathematical expressions

---

## Related Configuration Files

- **Skills.json**: Skill damage uses these formulas
- **Characters.json**: Character stats (ATK, DEF, SPD) feed into calculations
- **GearSets.json**: Set bonuses modify combat stats
- **Levels.json**: Stage definitions use victory/defeat conditions
- **ArenaConfig.json**: PvP uses combat rules and team composition limits

---

## Version History

- **1.0.0** (2025-11-23): Initial schema documentation
