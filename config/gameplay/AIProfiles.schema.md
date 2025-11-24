# AIProfiles.json Schema Documentation

## Overview
Defines AI behavior profiles using behavior trees, target prioritization systems, skill usage rules, and pattern-based decision making. Controls enemy AI, bot behavior, and NPC combat logic across PvE and PvP modes.

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

### `profiles` (array)
- **Type**: Array of AI Profile Objects
- **Description**: Complete AI behavior profile definitions
- **Required**: Yes
- **Usage**: Referenced by enemies, bots, NPCs in battles

---

## AI Profile Object

### `id` (string)
- **Format**: `AI_[CATEGORY]_[TYPE]`
- **Example**: `"AI_AGGRESSIVE_DPS"`, `"AI_BOSS_PATTERN"`
- **Description**: Unique AI profile identifier
- **Required**: Yes
- **Referenced by**: Enemy definitions in Levels.json, bot configurations

### `meta` (object)
Profile metadata and categorization.

#### `name` (string)
- **Type**: Display Text
- **Description**: Human-readable profile name
- **Example**: `"Aggressive DPS"`, `"Boss AI Pattern"`
- **Usage**: UI display and debugging

#### `description` (string)
- **Type**: Display Text
- **Description**: Profile behavior summary
- **Example**: `"Focuses on dealing maximum damage to priority targets"`
- **Usage**: Documentation and analytics

#### `tags` (array)
- **Type**: Array of Tag Strings
- **Description**: Profile categorization tags
- **Values**: `["Offensive", "Defensive", "Support", "Control", "Boss", "Pattern", "Adaptive"]`
- **Example**: `["Offensive", "DPS"]`
- **Usage**: Filtering and search

---

## Behavior Tree Object

### `root` (string)
- **Values**: `"Sequence"`, `"Selector"`, `"Parallel"`
- **Description**: Root node type of behavior tree
- **Required**: Yes

**Root Node Types**:
- **Sequence**: Execute children in order, stop on first failure
- **Selector**: Execute children until first success
- **Parallel**: Execute all children simultaneously

### `nodes` (array)
Array of behavior tree nodes.

---

## Behavior Tree Node Types

### Selector Node
```json
{
  "type": "Selector",
  "children": [...]
}
```
- **Description**: Tries each child until one succeeds
- **Usage**: Priority-based decision making
- **Example**: Try ultimate → skill → basic attack

### Sequence Node
```json
{
  "type": "Sequence",
  "children": [...]
}
```
- **Description**: Execute all children in order
- **Usage**: Multi-step actions
- **Example**: Check condition → perform action → verify result

### Condition Node
```json
{
  "type": "Condition",
  "check": "hasUltimate",
  "threshold": 0.7,
  "onTrue": {...}
}
```

#### `check` (string)
Condition to evaluate.

**Common Conditions**:
- **Player State**:
  - `hasUltimate` - Ultimate ready
  - `hasHighValueSkill` - High-value skill available
  - `hasDebuffSkill` - Debuff skill ready
  - `canApplyBuff` - Buff skill available
  - `energyFull` - Energy at 100%

- **Ally State**:
  - `allyHPCritical` - Ally HP below threshold
  - `allyHPLow` - Ally HP low
  - `allyNeedsBuff` - Ally missing buffs
  - `allyDebuffed` - Ally has debuffs
  - `teamHPLow` - Team average HP low

- **Enemy State**:
  - `enemyChargingUltimate` - Enemy about to use ultimate
  - `enemyHPLow` - Enemy HP low
  - `multipleEnemiesGrouped` - Enemies clustered
  - `enemyNotCCd` - Enemy not crowd controlled
  - `enemyHasNoBuff` - Enemy has no buffs

#### `threshold` (number)
- **Type**: Float (0.0-1.0)
- **Description**: HP/Energy percentage threshold
- **Example**: `0.3` = 30%, `0.6` = 60%
- **Optional**: Only for threshold-based conditions

#### `minCount` (number)
- **Type**: Integer
- **Description**: Minimum count for grouped enemies
- **Example**: `3` = at least 3 enemies grouped
- **Optional**: Only for count-based conditions

#### `onTrue` (object)
- **Type**: Action Node
- **Description**: Action to execute if condition is true
- **Required**: Yes

### Action Node
```json
{
  "type": "Action",
  "action": "UseUltimate",
  "targetSelection": "LowestHPEnemy"
}
```

#### `action` (string)
Action to perform.

**Combat Actions**:
- `UseUltimate` - Use ultimate skill
- `UseSkill` - Use regular skill
- `BasicAttack` - Basic attack
- `UseHeal` - Use healing skill
- `UseBuffSkill` - Apply buff to ally
- `UseCC` - Use crowd control
- `UseAoECC` - AoE crowd control
- `UseDebuff` - Apply debuff to enemy
- `SummonMinions` - Summon adds
- `BuffSelf` - Self-buff
- `EnrageMode` - Activate enrage

**Strategy Actions**:
- `Defensive` - Switch to defensive behavior
- `Aggressive` - Switch to aggressive behavior
- `Balanced` - Balanced behavior

#### `targetSelection` (string)
Target selection strategy.

**Enemy Targeting**:
- `LowestHPEnemy` - Enemy with lowest HP
- `HighestHPEnemy` - Enemy with highest HP
- `HighestThreatEnemy` - Highest threat level
- `HighestEnergyEnemy` - Most energy (close to ultimate)
- `NearestEnemy` - Closest enemy
- `RandomEnemy` - Random enemy
- `EnemyCluster` - Center of enemy group
- `AllEnemies` - All enemies (AoE)

**Ally Targeting**:
- `LowestHPAlly` - Ally with lowest HP
- `HighestDPSAlly` - Ally with highest damage output
- `Self` - Self-target

**Adaptive Targeting**:
- `BestTarget` - Calculate best target dynamically
- `Random` - Random target

#### `subBehavior` (string)
- **Type**: AI Profile ID
- **Description**: Reference to another AI profile
- **Example**: `"AI_DEFENSIVE_SUPPORT"`
- **Usage**: Behavior switching and composition

#### `count` (number)
- **Type**: Integer
- **Description**: Count for summon actions
- **Example**: `2` = summon 2 minions
- **Optional**: Only for summon actions

### Phase Selector Node
```json
{
  "type": "PhaseSelector",
  "phases": [...]
}
```
- **Description**: Select behavior based on HP phase
- **Usage**: Boss multi-phase fights

#### Phase Object

##### `name` (string)
- **Type**: Phase Name
- **Description**: Phase identifier
- **Example**: `"Phase1"`, `"Phase2"`, `"Phase3"`

##### `hpRange` (object)
HP range for this phase.

###### `min` (number)
- **Type**: Float (0.0-1.0)
- **Description**: Minimum HP percentage
- **Example**: `0.33` = 33% HP

###### `max` (number)
- **Type**: Float (0.0-1.0)
- **Description**: Maximum HP percentage
- **Example**: `0.66` = 66% HP

**Example**: `{"min": 0.33, "max": 0.66}` = Phase when HP is 33-66%

##### `onEnter` (object)
Action executed when entering phase.

###### `action` (string)
- **Description**: Action to perform on phase transition
- **Examples**: `"SummonMinions"`, `"EnrageMode"`, `"ChangeForm"`

###### `count` (number)
- **Optional**: Count for summon actions
- **Example**: `2` minions

###### `buffs` (array)
- **Type**: Array of Buff IDs
- **Description**: Buffs to apply on phase enter
- **Example**: `["BUFF_ENRAGE", "BUFF_DAMAGE_UP"]`

##### `behavior` (object)
Phase behavior pattern.

###### `type` (string)
- **Values**: `"Pattern"`, `"Adaptive"`, `"Reactive"`
- **Description**: Behavior type
- **Default**: `"Pattern"`

###### `actions` (array)
Pattern action sequence.

**Pattern Action Object**:
```json
{
  "turn": 1,
  "action": "BasicAttack",
  "target": "Random",
  "repeat": false
}
```

**`turn`** (number)
- **Type**: Integer
- **Description**: Turn number in pattern
- **Example**: `1`, `2`, `3`

**`action`** (string)
- **Description**: Action to perform
- **Example**: `"BasicAttack"`, `"Skill1"`, `"Ultimate"`

**`target`** (string)
- **Description**: Target selection
- **Example**: `"Random"`, `"LowestHP"`, `"All"`

**`repeat`** (boolean)
- **Description**: Whether pattern repeats from turn 1
- **Default**: `false`
- **True**: Loop pattern continuously

---

## Target Priority Object

### `weights` (object)
Priority weight assignments.

#### Common Weight Keys

##### Enemy Targeting Weights
- `lowHP` (number): Priority for low HP enemies
- `highThreat` (number): Priority for high threat enemies
- `squishy` (number): Priority for low-defense enemies
- `support` (number): Priority for support enemies
- `tank` (number): Priority for tank enemies
- `highEnergy` (number): Priority for enemies with high energy
- `notCCd` (number): Priority for non-CC'd enemies
- `noCCResistance` (number): Priority for CC-vulnerable enemies
- `clustered` (number): Priority for grouped enemies

##### Ally Targeting Weights
- `allyLowHP` (number): Priority for low HP allies
- `allyNeedsBuff` (number): Priority for allies missing buffs
- `allyDebuffed` (number): Priority for debuffed allies
- `selfPreservation` (number): Self-preservation priority

##### Tactical Weights
- `situational` (number): Situational priority
- `opportunistic` (number): Opportunistic targeting
- `balanced` (number): Balanced targeting

**Weight Examples**:
- `5.0` - High priority
- `3.0` - Medium priority
- `1.0` - Low priority
- `0.5` - Very low priority

### `roleModifiers` (object)
Role-based priority modifiers.

#### Keys
- `Support` (number): Modifier when targeting support role
- `DPS` (number): Modifier when targeting DPS role
- `Tank` (number): Modifier when targeting tank role

**Modifier Examples**:
- `1.5` - 50% higher priority
- `1.0` - Normal priority
- `0.5` - 50% lower priority

**Usage**:
```
Final Priority = Base Weight × Role Modifier

Example:
lowHP weight: 5.0
Target role: Support
roleModifier for Support: 1.5
Final Priority: 5.0 × 1.5 = 7.5
```

### `adaptiveWeighting` (boolean)
- **Description**: Dynamically adjust weights based on battle state
- **Default**: `false`
- **True**: Weights change based on situation
- **False**: Static weights

### `phaseDependent` (boolean)
- **Description**: Targeting changes by boss phase
- **Default**: `false`
- **Usage**: Boss AI only

---

## Skill Usage Rules Object

### Ultimate Rules
```json
{
  "ultimate": {
    "useWhen": "energyFull",
    "targetCount": "max",
    "saveFor": null
  }
}
```

#### `useWhen` (string)
- **Values**: `"energyFull"`, `"alwaysAvailable"`, `"criticalMoment"`, `"neverUse"`
- **Description**: When to use ultimate
- **Default**: `"energyFull"`

#### `targetCount` (string)
- **Values**: `"max"`, `"single"`, `"optimal"`
- **Description**: Target count optimization
- **Default**: `"max"`

#### `saveFor` (string | null)
- **Type**: Condition name or null
- **Description**: Save ultimate for specific condition
- **Example**: `"bossBelowHalfHP"`, `null` = use when ready

### Skill Rules
```json
{
  "skill": {
    "useWhen": "onCooldown",
    "prioritizeAoE": true,
    "minTargets": 1
  }
}
```

#### `useWhen` (string)
- **Values**: `"onCooldown"`, `"strategically"`, `"saveForUltCombo"`
- **Description**: When to use skills
- **Default**: `"onCooldown"`

#### `prioritizeAoE` (boolean)
- **Description**: Prefer AoE skills over single-target
- **Default**: `false`

#### `minTargets` (number)
- **Type**: Integer
- **Description**: Minimum targets for AoE skills
- **Example**: `1` = use even on single target, `3` = only if 3+ targets

### Basic Attack Rules
```json
{
  "basicAttack": {
    "fallback": true
  }
}
```

#### `fallback` (boolean)
- **Description**: Use basic attack as fallback
- **Default**: `true`
- **True**: Always available when no skills ready

### Heal Rules
```json
{
  "heal": {
    "useWhen": "allyHPBelow",
    "threshold": 0.7,
    "targetSelection": "LowestHPAlly"
  }
}
```

#### `threshold` (number)
- **Type**: Float (0.0-1.0)
- **Description**: HP threshold for healing
- **Example**: `0.7` = heal when ally below 70% HP

### Buff Rules
```json
{
  "buff": {
    "useWhen": "allyNeedsBuff",
    "targetSelection": "HighestDPSAlly",
    "preventOverwrite": true
  }
}
```

#### `preventOverwrite` (boolean)
- **Description**: Don't buff if buff already active
- **Default**: `true`
- **Usage**: Prevent buff waste

### Cleanse Rules
```json
{
  "cleanse": {
    "useWhen": "allyDebuffed",
    "debuffCountThreshold": 2
  }
}
```

#### `debuffCountThreshold` (number)
- **Type**: Integer
- **Description**: Minimum debuffs before cleansing
- **Example**: `2` = cleanse when ally has 2+ debuffs

### CC (Crowd Control) Rules
```json
{
  "cc": {
    "useWhen": "enemyNotCCd",
    "targetSelection": "HighestThreatEnemy",
    "checkResistance": true
  }
}
```

#### `checkResistance` (boolean)
- **Description**: Check enemy CC resistance before using
- **Default**: `true`
- **True**: Don't waste CC on resistant enemies

### Debuff Rules
```json
{
  "debuff": {
    "useWhen": "enemyHasNoBuff",
    "stackDebuffs": true,
    "maxStacksPerTarget": 3
  }
}
```

#### `stackDebuffs` (boolean)
- **Description**: Stack multiple debuffs on same target
- **Default**: `false`

#### `maxStacksPerTarget` (number)
- **Type**: Integer
- **Description**: Maximum debuff stacks per target
- **Example**: `3` = max 3 debuff stacks

### AoE Rules
```json
{
  "aoe": {
    "useWhen": "multipleTargets",
    "minTargets": 3,
    "targetSelection": "EnemyCluster"
  }
}
```

### Adaptive Rules
```json
{
  "adaptive": true,
  "evaluateEachTurn": true,
  "switchStrategy": {
    "healthThreshold": 0.5,
    "turnThreshold": 10
  }
}
```

#### `adaptive` (boolean)
- **Description**: Adapt behavior dynamically
- **Default**: `false`
- **Usage**: Balanced/adaptive profiles only

#### `evaluateEachTurn` (boolean)
- **Description**: Re-evaluate strategy every turn
- **Default**: `false`
- **True**: Dynamic decision making each turn

#### `switchStrategy` (object)
Strategy switching conditions.

##### `healthThreshold` (number)
- **Type**: Float (0.0-1.0)
- **Description**: HP threshold for strategy switch
- **Example**: `0.5` = switch when below 50% HP

##### `turnThreshold` (number)
- **Type**: Integer
- **Description**: Turn number for strategy switch
- **Example**: `10` = switch after turn 10

### Phase Pattern Rules
```json
{
  "phasePattern": true,
  "enrageThreshold": 0.33
}
```

#### `phasePattern` (boolean)
- **Description**: Use phase-based patterns
- **Default**: `false`
- **Usage**: Boss AI only

#### `enrageThreshold` (number)
- **Type**: Float (0.0-1.0)
- **Description**: HP threshold for enrage mode
- **Example**: `0.33` = enrage below 33% HP

---

## AI Profile Examples

### Aggressive DPS Profile
```json
{
  "id": "AI_AGGRESSIVE_DPS",
  "meta": {
    "name": "Aggressive DPS",
    "tags": ["Offensive", "DPS"]
  },
  "targetPriority": {
    "weights": {
      "lowHP": 5.0,
      "support": 4.0,
      "tank": 0.5
    }
  },
  "skillUsageRules": {
    "ultimate": {
      "useWhen": "energyFull",
      "targetCount": "max"
    }
  }
}
```

**Behavior**:
- Prioritize low HP enemies (weight 5.0)
- Focus supports (weight 4.0)
- Ignore tanks (weight 0.5)
- Use ultimate immediately when ready
- Always attack lowest HP enemy

**Usage**: DPS enemies, aggressive bots

### Defensive Support Profile
```json
{
  "id": "AI_DEFENSIVE_SUPPORT",
  "meta": {
    "name": "Defensive Support",
    "tags": ["Defensive", "Support"]
  },
  "targetPriority": {
    "weights": {
      "allyLowHP": 10.0,
      "allyDebuffed": 7.0
    }
  },
  "skillUsageRules": {
    "heal": {
      "useWhen": "allyHPBelow",
      "threshold": 0.7
    }
  }
}
```

**Behavior**:
- Heal allies below 70% HP
- Prioritize critically wounded allies (weight 10.0)
- Cleanse debuffs (weight 7.0)
- Buff high DPS allies

**Usage**: Support enemies, healer bots

### Tactical Controller Profile
```json
{
  "id": "AI_TACTICAL_CONTROLLER",
  "meta": {
    "name": "Tactical Controller",
    "tags": ["Control", "Tactical"]
  },
  "targetPriority": {
    "weights": {
      "highEnergy": 5.0,
      "notCCd": 4.0
    }
  },
  "skillUsageRules": {
    "cc": {
      "useWhen": "enemyNotCCd",
      "checkResistance": true
    }
  }
}
```

**Behavior**:
- Interrupt enemies charging ultimate
- AoE CC when 3+ enemies grouped
- Apply debuffs to highest threat
- Check CC resistance before using

**Usage**: Control enemies, tactical bots

### Boss Pattern Profile
```json
{
  "id": "AI_BOSS_PATTERN",
  "behaviorTree": {
    "type": "PhaseSelector",
    "phases": [
      {
        "name": "Phase1",
        "hpRange": {"min": 0.66, "max": 1.0},
        "behavior": {
          "actions": [
            {"turn": 1, "action": "BasicAttack"},
            {"turn": 2, "action": "BasicAttack"},
            {"turn": 3, "action": "Skill1"},
            {"repeat": true}
          ]
        }
      },
      {
        "name": "Phase2",
        "hpRange": {"min": 0.33, "max": 0.66},
        "onEnter": {
          "action": "SummonMinions",
          "count": 2
        }
      },
      {
        "name": "Phase3",
        "hpRange": {"min": 0.0, "max": 0.33},
        "onEnter": {
          "action": "EnrageMode",
          "buffs": ["BUFF_ENRAGE"]
        }
      }
    ]
  }
}
```

**Behavior**:
- **Phase 1 (100-66% HP)**: Basic attack pattern
- **Phase 2 (66-33% HP)**: Summon 2 minions, AoE attacks
- **Phase 3 (33-0% HP)**: Enrage mode, ultimate spam

**Usage**: Boss encounters, raid bosses

---

## Behavior Tree Execution Examples

### Example 1: Aggressive DPS Decision
```
Turn Start:
1. Check: hasUltimate?
   → Yes: energyFull (100/100)
   → Action: UseUltimate on LowestHPEnemy
   → Target: Enemy Mage (30% HP)
   → Execute ultimate skill

Result: Ultimate used on lowest HP target
```

### Example 2: Support Healing Decision
```
Turn Start:
1. Check: allyHPCritical? (threshold 30%)
   → No: All allies above 30%
2. Check: allyHPLow? (threshold 60%)
   → Yes: Warrior at 45% HP
   → Action: UseSkill on LowestHPAlly
   → Target: Warrior (45% HP)
   → Execute heal skill

Result: Heal used on ally below 60% HP
```

### Example 3: Controller Interrupt
```
Turn Start:
1. Check: enemyChargingUltimate?
   → Yes: Enemy Mage at 98/100 energy
   → Action: UseCC on HighestEnergyEnemy
   → Target: Enemy Mage (98 energy)
   → Execute stun skill

Result: Successfully interrupt enemy ultimate
```

### Example 4: Boss Phase Transition
```
Boss HP: 66% → 65% (crosses phase threshold)

Phase Transition:
1. Exit Phase1
2. Enter Phase2
3. onEnter: SummonMinions (count: 2)
   → Spawn 2 minion enemies
4. Switch behavior pattern:
   Turn 1: AoESkill → AllEnemies
   Turn 2: BuffSelf → Self
   Turn 3: Skill2 → LowestHP
   Repeat from Turn 1

Result: Boss now in Phase2 with new behavior
```

---

## Target Priority Calculation

### Formula
```
Priority Score = Base Weight × Role Modifier × Situational Modifier

Final Target = Highest Priority Score
```

### Example Calculation
```
Scenario: Aggressive DPS choosing target

Targets:
1. Enemy Tank (80% HP, Tank role)
2. Enemy Mage (30% HP, DPS role)
3. Enemy Healer (90% HP, Support role)

Weights:
- lowHP: 5.0
- support: 4.0
- tank: 0.5

Role Modifiers:
- Tank: 0.5
- DPS: 1.2
- Support: 1.5

Calculation:
Tank: 0.5 (tank weight) × 0.5 (role) = 0.25
Mage: 5.0 (lowHP weight) × 1.2 (role) = 6.0
Healer: 4.0 (support weight) × 1.5 (role) = 6.0

Tiebreaker: HP percentage
Mage: 30% HP < Healer: 90% HP

Final Target: Enemy Mage (priority 6.0, lowest HP)
```

---

## Boss Phase Pattern Examples

### Simple 3-Phase Boss
```
Phase 1 (100-66%): Learning phase
- Basic attacks
- Simple skill pattern
- No summons

Phase 2 (66-33%): Escalation
- Summon minions
- AoE attacks
- Buff self

Phase 3 (33-0%): Enrage
- Rapid ultimates
- High damage
- Aggressive targeting
```

### Complex Multi-Phase Boss
```
Phase 1 (100-75%): Test player skill
- Pattern: Attack → Attack → Skill → Repeat
- Target: Random

Phase 2 (75-50%): Introduce mechanics
- onEnter: Shield self
- Pattern: AoE → Debuff → Attack → Repeat
- Target: Highest threat

Phase 3 (50-25%): Summon phase
- onEnter: Summon 3 adds
- Pattern: Buff adds → AoE → Heal adds → Repeat
- Target: Prioritize adds alive

Phase 4 (25-0%): Desperation
- onEnter: Enrage, remove shield
- Pattern: Ultimate → Ultimate → AoE → Repeat
- Target: All enemies
```

---

## Data Validation Rules

1. **Profile IDs**:
   - Must be unique across all profiles
   - Format: AI_[CATEGORY]_[TYPE]

2. **Behavior Tree Structure**:
   - root must be valid node type
   - nodes array must not be empty
   - Condition nodes must have onTrue

3. **Target Priority Weights**:
   - All weights must be ≥ 0
   - Typical range: 0.0-10.0
   - Role modifiers: 0.1-3.0 (reasonable range)

4. **Skill Usage Thresholds**:
   - All thresholds must be 0.0-1.0
   - minTargets must be ≥ 1
   - maxStacksPerTarget: 1-10

5. **Boss Phases**:
   - hpRange.min < hpRange.max
   - Phases should cover 0.0-1.0 (0-100% HP)
   - No overlapping HP ranges
   - At least 1 phase required

6. **Pattern Actions**:
   - turn numbers must be sequential
   - At least 1 action required
   - Only last action can have repeat: true

7. **Conditional Logic**:
   - check must be valid condition name
   - threshold required for threshold-based conditions
   - minCount required for count-based conditions

---

## Design Guidelines

### AI Personality
- **Aggressive**: High damage, low HP targets, fast kills
- **Defensive**: Protect allies, heal, survival focus
- **Tactical**: Control, debuffs, strategic timing
- **Balanced**: Adapt to situation, flexible

### Target Priority Design
- **DPS AI**: Priority → Supports > Low HP > DPS > Tanks
- **Support AI**: Priority → Low HP Allies > Debuffed Allies > Self
- **Tank AI**: Priority → Protect Allies > Taunt Enemies > Survive
- **Boss AI**: Priority → Phase-dependent, pattern-based

### Skill Usage Strategy
- **Conservative**: Save skills for optimal moments
- **Aggressive**: Use skills on cooldown
- **Adaptive**: Evaluate each turn
- **Pattern**: Fixed pattern for predictability

### Boss Design
- **Phase 1**: Teach mechanics (easy)
- **Phase 2**: Challenge players (medium)
- **Phase 3**: Test mastery (hard)
- **Enrage**: Time pressure (very hard)

### Difficulty Scaling
- **Easy**: Simple patterns, low weights, basic attacks
- **Normal**: Mixed patterns, balanced weights, skill usage
- **Hard**: Complex patterns, high weights, optimal play
- **Expert**: Adaptive AI, perfect targeting, counter strategies

---

## Performance Considerations

### Behavior Tree Depth
```
Shallow tree (depth 2-3):
- Fast evaluation
- Simple AI
- Good for common enemies

Deep tree (depth 5+):
- Slower evaluation
- Complex AI
- Reserved for bosses
```

### Target Priority Calculation
```
Lightweight (3-5 weights):
- Fast calculation
- Sufficient for most AI
- Recommended

Heavy (10+ weights):
- Slow calculation
- Very precise targeting
- Boss AI only
```

### Adaptive AI
```
Static AI:
- Pre-computed decisions
- No runtime evaluation
- Fastest

Adaptive AI:
- Evaluate each turn
- Dynamic decisions
- Slower but smarter
```

---

## Related Configuration Files

- **Skills.json**: Skills referenced by AI actions
- **Characters.json**: Character stats affecting AI decisions
- **BattleRules.json**: Combat rules AI follows
- **Levels.json**: Enemy AI profile assignments
- **Matchmaking.json**: Bot AI difficulty scaling
- **SystemSettings.json**: AI performance settings

---

## Version History
- **1.0.0** (2025-11-23): Initial schema documentation
