# GrowthCurves.json Schema Documentation

## Overview
This document provides comprehensive descriptions for all indices (fields) in the `GrowthCurves.json` configuration file. This file defines mathematical curves for character stat progression, experience requirements, and equipment enhancement costs across different tiers and categories.

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

### `curves` (array)
- **Type**: Array of Growth Curve Objects
- **Description**: Stat scaling curves for characters and equipment
- **Required**: Yes

### `exponentialCurves` (array)
- **Type**: Array of Exponential Curve Objects
- **Description**: Curves for costs and requirements that scale exponentially
- **Required**: Yes
- **Usage**: Experience requirements, enhancement costs, price scaling

---

## Growth Curve Object Structure

Growth curves define how character stats scale from level 1 to max level.

### `id` (string)
- **Type**: Unique Identifier
- **Description**: Curve identifier referenced by character configurations
- **Format**: `CURVE_[CATEGORY]_[TIER]`
- **Examples**:
  - `"CURVE_WARRIOR_S_TIER"` - SSR warrior stat scaling
  - `"CURVE_MAGE_S_TIER"` - SSR mage stat scaling
  - `"CURVE_SUPPORT_A_TIER"` - SR support stat scaling
  - `"CURVE_COMMON_B_TIER"` - R character stat scaling
- **Required**: Yes
- **Constraints**: Must be unique across all curves

---

## Meta Object (Curve Metadata)

### `meta` (object)
Contains descriptive metadata for the growth curve.

#### `meta.name` (string)
- **Type**: Display Name
- **Description**: Human-readable curve name
- **Example**: `"S-Tier Warrior Growth"`
- **Usage**: Developer reference, admin tools

#### `meta.description` (string)
- **Type**: Detailed Description
- **Description**: Explanation of curve purpose and usage
- **Example**: `"High-end warrior stat scaling for SSR characters"`
- **Usage**: Documentation, balancing notes

#### `meta.category` (string)
- **Type**: Character Archetype
- **Description**: Type of character this curve is designed for
- **Values**: `"Warrior"`, `"Mage"`, `"Support"`, `"Tank"`, `"Assassin"`, `"Common"`
- **Usage**: Curve selection based on character role

#### `meta.tier` (string)
- **Type**: Rarity Tier
- **Description**: Character rarity level this curve applies to
- **Values**: `"SSR"`, `"SR"`, `"R"`
- **Usage**: Determines stat growth strength
- **Balance**: Higher tiers have stronger scaling multipliers

---

## Max Level Field

### `maxLevel` (number)
- **Type**: Integer
- **Description**: Maximum character level for this curve
- **Range**: 60-80
- **Typical Values**:
  - `80` - SSR characters
  - `80` - SR characters
  - `70` - R characters
- **Usage**: Curve validation, level cap enforcement

---

## Stats Object

### `stats` (object)
Contains scaling formulas for each character stat.

**Structure**: Keys are stat names (`"hp"`, `"atk"`, `"def"`, `"spd"`), values are stat curve objects.

---

### Stat Curve Object

Each stat has its own growth curve configuration.

#### `formula` (string)
- **Type**: Mathematical Formula String
- **Description**: Expression defining stat value at any level
- **Variables**:
  - `base` - Base stat value from character's baseStats
  - `level` - Current character level
- **Operators**: `*`, `+`, `-`, `/`, `()`, `pow()`
- **Common Formulas**:
  - `"base * (1 + (level - 1) * 0.08)"` - 8% growth per level
  - `"base * (1 + (level - 1) * 0.06)"` - 6% growth per level
  - `"base * (1 + (level - 1) * 0.05)"` - 5% growth per level
  - `"base"` - No growth (flat stat)

**Formula Interpretation**:
```
Stat at Level X = base * (1 + (X - 1) * growth_rate)

Example: HP with 0.08 growth rate
Level 1:  HP = 850 * (1 + (1-1) * 0.08) = 850 * 1.0 = 850
Level 10: HP = 850 * (1 + (10-1) * 0.08) = 850 * 1.72 = 1462
Level 80: HP = 850 * (1 + (80-1) * 0.08) = 850 * 7.32 = 6222
```

#### `breakpoints` (array)
- **Type**: Array of Breakpoint Objects
- **Description**: Pre-calculated stat values at specific levels for quick lookup
- **Usage**: Performance optimization, validation checkpoints

##### Breakpoint Object Fields:

###### `level` (number)
- **Type**: Integer
- **Description**: Character level for this checkpoint
- **Common Values**: `1`, `20`, `40`, `60`, `80`

###### `multiplier` (number)
- **Type**: Float
- **Description**: Stat multiplier at this level (relative to base)
- **Format**: `1.0` = base value, `2.5` = 2.5x base value
- **Example**: `7.32` at level 80 means stat is 7.32x stronger than level 1

**Calculation**:
```
Actual Stat Value = base_stat * multiplier
Example: base ATK 120, multiplier 5.74 → Final ATK = 120 * 5.74 = 688.8
```

---

### Common Stat Growth Patterns

#### HP (Health Points)
- **Warrior/Tank**: `0.08` growth (highest HP scaling)
- **Mage**: `0.06` growth (moderate HP scaling)
- **Support**: `0.07` growth (moderate-high HP scaling)
- **Common**: `0.055` growth (lower HP scaling)
- **Rationale**: Tanks need high HP to survive, mages are fragile

#### ATK (Attack Power)
- **Warrior**: `0.06` growth (moderate ATK scaling)
- **Mage**: `0.08` growth (highest ATK scaling)
- **Support**: `0.05` growth (lowest ATK scaling)
- **Common**: `0.05` growth (lower ATK scaling)
- **Rationale**: Mages are primary damage dealers, supports focus on utility

#### DEF (Defense)
- **Warrior**: `0.07` growth (high DEF scaling)
- **Mage**: `0.05` growth (lowest DEF scaling)
- **Support**: `0.06` growth (moderate DEF scaling)
- **Common**: `0.05` growth (lower DEF scaling)
- **Rationale**: Warriors are frontline, need high defense

#### SPD (Speed)
- **All Classes**: `"base"` formula (no growth)
- **Multiplier**: `1.0` at all levels
- **Rationale**: Speed is a tactical stat that shouldn't scale with level
- **Note**: Can be increased through equipment and buffs

---

## Exponential Curve Object Structure

Exponential curves define costs and requirements that increase dramatically with progression.

### `id` (string)
- **Type**: Unique Identifier
- **Description**: Exponential curve identifier
- **Format**: `CURVE_[PURPOSE]`
- **Examples**:
  - `"CURVE_EXP_REQUIRED"` - Experience needed per level
  - `"CURVE_ENHANCEMENT_COST"` - Equipment upgrade gold cost
  - `"CURVE_ASCENSION_COST"` - Character ascension material cost

---

## Meta Object (Exponential Curve Metadata)

### `meta` (object)

#### `meta.name` (string)
- **Type**: Display Name
- **Description**: Human-readable curve name
- **Example**: `"Experience Required per Level"`

#### `meta.description` (string)
- **Type**: Detailed Description
- **Description**: Explanation of what this curve calculates
- **Example**: `"Exponential curve for EXP needed to level up"`

---

## Formula Field (Exponential)

### `formula` (string)
- **Type**: Mathematical Formula String
- **Description**: Expression defining exponential growth
- **Variables**:
  - `level` - Current level or tier
- **Functions**:
  - `pow(base, exponent)` - Exponentiation
  - `floor(value)` - Round down to integer
- **Common Patterns**:
  - `"floor(100 * pow(1.1, level - 1))"` - 10% compound growth
  - `"floor(500 * pow(1.15, level))"` - 15% compound growth
  - `"floor(base * pow(multiplier, level))"` - General exponential

**Example**: Experience Required
```
formula: "floor(100 * pow(1.1, level - 1))"

Level 1:  100 * 1.1^0  = 100 EXP
Level 10: 100 * 1.1^9  = 236 EXP
Level 20: 100 * 1.1^19 = 673 EXP
Level 50: 100 * 1.1^49 = 11739 EXP
Level 80: 100 * 1.1^79 = 204840 EXP
```

---

## Breakpoints Array (Exponential)

### `breakpoints` (array)
- **Type**: Array of Value Checkpoint Objects
- **Description**: Pre-calculated values at specific levels

#### Breakpoint Object Fields:

##### `level` (number)
- **Type**: Integer
- **Description**: Level/tier for this checkpoint
- **Example**: `1`, `10`, `20`, `30`, etc.

##### `value` (number)
- **Type**: Integer
- **Description**: Calculated result at this level
- **Example**: `100`, `236`, `673`, `1745`, etc.

---

## Curve Usage Examples

### Character Stat Calculation

**Given**: Fire Dragon character at level 50
- Base HP: 850 (from Characters.json)
- Curve: CURVE_WARRIOR_S_TIER
- HP Formula: `base * (1 + (level - 1) * 0.08)`
- Level 50 Multiplier: `1 + (50-1) * 0.08 = 4.92`

**Calculation**:
```
HP at Level 50 = 850 * 4.92 = 4182 HP
```

**Verification with Breakpoints**:
```
Level 40 multiplier: 4.12 → 850 * 4.12 = 3502 HP
Level 60 multiplier: 5.72 → 850 * 5.72 = 4862 HP
Level 50 is between these values ✓
```

---

### Experience Required Calculation

**Given**: Character leveling from 49 to 50
- Formula: `floor(100 * pow(1.1, level - 1))`

**Calculation**:
```
EXP needed for Level 50 = floor(100 * pow(1.1, 49))
                        = floor(100 * 117.39)
                        = floor(11739)
                        = 11739 EXP
```

**Total EXP to reach Level 50**:
```
Sum of all levels 1-49 EXP requirements
(Use breakpoints to estimate or sum formula results)
```

---

### Enhancement Cost Calculation

**Given**: Enhancing weapon from +9 to +10
- Formula: `floor(500 * pow(1.15, level))`

**Calculation**:
```
Cost for Enhancement +10 = floor(500 * pow(1.15, 10))
                         = floor(500 * 4.0456)
                         = floor(2022.8)
                         = 2022 Gold

(Breakpoint shows 2027 Gold, formula may vary slightly)
```

---

## Curve Design Guidelines

### Growth Rate Selection

#### Slow Growth (0.05)
- **Usage**: Secondary stats, support characters
- **Result**: Stat roughly 5x stronger at level 80
- **Example**: Support ATK, Common tier stats

#### Moderate Growth (0.06-0.07)
- **Usage**: Balanced stats, mid-tier characters
- **Result**: Stat roughly 5-7x stronger at level 80
- **Example**: Warrior HP, Mage DEF

#### Fast Growth (0.08)
- **Usage**: Primary stats, SSR characters
- **Result**: Stat roughly 7-8x stronger at level 80
- **Example**: Warrior HP, Mage ATK

#### Flat Growth (base)
- **Usage**: Tactical stats that shouldn't scale
- **Result**: Stat remains constant
- **Example**: Speed (SPD)

---

### Exponential Curve Balancing

#### Gentle Exponential (1.05-1.10 base)
- **Usage**: Gradual cost increase
- **Result**: Cost doubles every 10-15 levels
- **Example**: Basic enhancement costs

#### Moderate Exponential (1.10-1.15 base)
- **Usage**: Standard progression gating
- **Result**: Cost doubles every 5-7 levels
- **Example**: Experience requirements, standard enhancement

#### Steep Exponential (1.15-1.25 base)
- **Usage**: End-game cost scaling, premium upgrades
- **Result**: Cost doubles every 3-5 levels
- **Example**: High-tier ascension materials

---

## Breakpoint Generation

Breakpoints should be calculated at key milestone levels:

### Standard Breakpoints
- **Level 1**: Starting point (always 1.0 or base)
- **Level 20**: Early game checkpoint
- **Level 40**: Mid game checkpoint
- **Level 60**: Late game checkpoint
- **Level 80**: Max level (endgame)

### Additional Checkpoints
- **Level 10**: Early progression
- **Level 30**: Early-to-mid transition
- **Level 50**: Mid-to-late transition
- **Level 70**: Pre-max checkpoint

---

## Data Validation Rules

1. **Unique IDs**: All curve IDs must be unique
2. **Formula Syntax**: All formulas must be valid mathematical expressions
3. **Breakpoint Ordering**: Breakpoints must be in ascending level order
4. **Multiplier Consistency**: Breakpoint multipliers should match formula results
5. **Growth Rate Sanity**: Growth rates typically between 0.03 and 0.10
6. **Max Level Consistency**: maxLevel should match highest breakpoint level
7. **Non-Negative Values**: All calculated values should be positive
8. **Integer Results**: floor() should be used for discrete values (EXP, gold)

---

## Curve Selection Matrix

| Character Type | Rarity | Curve ID | HP Growth | ATK Growth | DEF Growth |
|---------------|--------|----------|-----------|------------|------------|
| Warrior | SSR | CURVE_WARRIOR_S_TIER | 0.08 | 0.06 | 0.07 |
| Mage | SSR | CURVE_MAGE_S_TIER | 0.06 | 0.08 | 0.05 |
| Support | SR | CURVE_SUPPORT_A_TIER | 0.07 | 0.05 | 0.06 |
| Any | R | CURVE_COMMON_B_TIER | 0.055 | 0.05 | 0.05 |

---

## Performance Optimization

### Breakpoint Usage
- **Exact Match**: If level matches breakpoint, use stored multiplier directly
- **Interpolation**: For levels between breakpoints, interpolate or calculate
- **Caching**: Cache calculated values to avoid repeated computation

### Formula Evaluation
```javascript
// Pseudocode for stat calculation
function getStatAtLevel(baseStat, level, curve) {
  // Try exact breakpoint match first
  breakpoint = curve.breakpoints.find(bp => bp.level === level)
  if (breakpoint) {
    return baseStat * breakpoint.multiplier
  }

  // Calculate using formula
  return evaluateFormula(curve.formula, baseStat, level)
}
```

---

## Common Use Cases

### Character Level-Up Stat Preview
1. Get character's base stats from Characters.json
2. Determine curve ID based on rarity and role
3. Calculate each stat at target level using formula
4. Display projected stats to player

### Experience Bar Display
1. Get current level and EXP
2. Look up EXP required for next level in CURVE_EXP_REQUIRED
3. Calculate percentage: `(currentEXP / requiredEXP) * 100`
4. Display progress bar

### Enhancement Cost Estimation
1. Get current equipment enhancement level
2. Get target enhancement level
3. Sum costs from current to target using CURVE_ENHANCEMENT_COST
4. Display total gold and materials needed

---

## Related Configuration Files

- **Characters.json**: Uses curves for character stat progression (via `growthCurves`)
- **Items.json**: Equipment stats may scale with level using similar curves
- **Skills.json**: Skill damage formulas reference character stats affected by curves
- **Economy.json**: Uses exponential curves for pricing and costs

---

## Version History

- **1.0.0** (2025-11-23): Initial schema documentation
