
---

# 📘 **Skills.json Configuration Explanation**

This file defines the **Universal Skill System** for the game, where all character abilities, attacks, and actions are data-driven. The Skill Execution Engine reads these configurations at runtime to execute complex combat mechanics without hardcoded logic.

---

## 🧩 **1. Overall Structure**

```json
{
  "$schema": "...",
  "version": "1.0.0",
  "lastUpdated": "2025-11-23T00:00:00Z",
  "skills": [...],
  "statusEffects": [...]
}
```

### Key Components:
- **skills[]**: Active abilities (attacks, ultimates, passives)
- **statusEffects[]**: Buffs, debuffs, DoTs (Damage over Time)

---

## 🗡️ **2. Skill Definition Structure**

Each skill contains:

| Section | Purpose |
|---------|---------|
| **id** | Unique identifier (format: SKILL_*) |
| **meta** | Display information and categorization |
| **activation** | Cooldown, cost, casting requirements |
| **targeting** | How the skill selects targets |
| **timeline** | Frame-by-frame execution sequence |
| **assets** | Icon and VFX references (Addressables) |

---

## 📋 **3. Detailed Field Explanations**

### 3.1 Meta Information

```json
"meta": {
  "nameKey": "SKILL_NAME_PHOENIX_STRIKE",
  "descriptionKey": "SKILL_DESC_PHOENIX_STRIKE",
  "icon": "Icon_Skill_PhoenixStrike",
  "tags": ["Fire", "Melee", "Burst", "Ultimate"]
}
```

| Field | Purpose |
|-------|---------|
| **nameKey** | Localization key for skill name |
| **descriptionKey** | Localization key for description |
| **icon** | Icon asset reference |
| **tags** | Gameplay tags (element, type, category) |

**Tags Usage:**
- **Element**: Fire, Ice, Light, Dark, Nature, Wind, Physical
- **Type**: Melee, Ranged, AoE, Single
- **Category**: Burst, Control, Heal, Buff, Debuff
- **Tier**: Basic, Skill, Ultimate, Passive

---

### 3.2 Activation Rules

```json
"activation": {
  "cooldown": 12.0,
  "initialCooldown": 0.0,
  "cost": [
    {"resource": "Mana", "value": 50},
    {"resource": "Energy", "value": 100}
  ]
}
```

| Field | Meaning |
|-------|---------|
| **cooldown** | Turns/seconds before skill can be used again |
| **initialCooldown** | Cooldown at battle start (0 = usable immediately) |
| **cost[]** | Resources consumed when casting |

**Resource Types:**
- **Mana**: Regenerates each turn
- **Energy**: Ultimate gauge (builds from damage/taking hits)
- **HP**: Skills that cost health
- **Rage**: Warrior-specific resource

---

### 3.3 Targeting System

```json
"targeting": {
  "mode": "TargetUnit",
  "range": 4.0,
  "filter": {
    "team": "Enemy",
    "sort": "LowestHP",
    "maxTargets": 1
  },
  "aoe": {
    "enabled": true,
    "shape": "Circle",
    "radius": 2.0,
    "centerOn": "Target"
  }
}
```

#### Targeting Modes:
- **TargetUnit**: Single target selection
- **TargetArea**: Ground-targeted AoE
- **TargetSelf**: Self-cast only
- **TargetAllAllies**: All friendly units
- **TargetAllEnemies**: All enemy units

#### Filter Options:
| Filter | Effect |
|--------|--------|
| **team** | Enemy, Ally, All |
| **sort** | LowestHP, HighestHP, LowestDef, Random, Closest |
| **maxTargets** | Number of targets hit |
| **excludeSelf** | Exclude caster from AoE |

#### AoE Shapes:
- **Circle**: Radius from center point
- **Line**: Straight line from caster
- **Cone**: Arc in front of caster
- **Cross**: Plus-shaped pattern

---

### 3.4 Timeline Execution

The **timeline** is the core of the skill system. It defines **when** each action happens during skill execution.

```json
"timeline": [
  {
    "time": 0.0,
    "action": "PlayAnim",
    "param": "Attack_Ultimate_Phoenix"
  },
  {
    "time": 0.5,
    "action": "ApplyEffects",
    "payload": [...]
  }
]
```

#### Timeline Actions:

| Action | Purpose | Example Param |
|--------|---------|---------------|
| **PlayAnim** | Trigger character animation | "Attack_Ultimate_Phoenix" |
| **SpawnVFX** | Create visual effect | "VFX_Phoenix_Charge" |
| **ApplyEffects** | Deal damage, apply status | payload array |
| **CameraShake** | Screen shake effect | {intensity, duration} |
| **PlaySound** | Audio effect | "SFX_Phoenix_Strike" |
| **Dash** | Move caster/target | {distance, duration} |
| **Projectile** | Spawn projectile | {speed, model} |

#### Time Format:
- **0.0**: Skill activation frame
- **0.5**: 0.5 seconds after activation
- Allows frame-perfect synchronization of VFX, damage, and animations

---

### 3.5 Effect Payloads

Effects are applied at specific timeline moments:

#### Damage Effect:
```json
{
  "type": "Damage",
  "formula": "(caster.atk * 3.5) + (target.maxHp * 0.05)",
  "element": "Fire",
  "canCrit": true,
  "damageType": "Physical"
}
```

**Formula Variables:**
- `caster.atk`, `caster.def`, `caster.hp`, `caster.maxHp`
- `target.atk`, `target.def`, `target.hp`, `target.maxHp`
- Mathematical operators: `+`, `-`, `*`, `/`, `pow()`, `min()`, `max()`

**Damage Types:**
- **Physical**: Reduced by DEF
- **Magical**: Reduced by RES
- **True**: Ignores defense

#### Status Effect:
```json
{
  "type": "ApplyStatus",
  "id": "STATUS_BURN",
  "duration": 3,
  "chance": 0.8,
  "stacks": 2
}
```

| Field | Meaning |
|-------|---------|
| **id** | Status effect ID (references statusEffects[]) |
| **duration** | Turns/seconds the status lasts |
| **chance** | Probability (0.8 = 80% chance) |
| **stacks** | Number of stacks applied |

#### Heal Effect:
```json
{
  "type": "Heal",
  "formula": "(caster.atk * 2.0) + 500",
  "canCrit": true
}
```

#### Buff/Debuff:
```json
{
  "type": "ApplyBuff",
  "id": "BUFF_ATK_UP",
  "duration": 2,
  "value": 0.3
}
```

---

## 🔥 **4. Status Effects System**

### 4.1 Status Effect Structure

```json
{
  "id": "STATUS_BURN",
  "meta": {
    "nameKey": "STATUS_NAME_BURN",
    "descriptionKey": "STATUS_DESC_BURN",
    "icon": "Icon_Status_Burn",
    "type": "Debuff",
    "category": "DoT"
  },
  "duration": 3,
  "stackable": true,
  "maxStacks": 5,
  "effects": [
    {
      "type": "DamageOverTime",
      "formula": "(caster.atk * 0.5)",
      "element": "Fire",
      "tickInterval": 1.0
    }
  ],
  "visual": {
    "vfx": "VFX_Status_Burn",
    "color": "#FF4500"
  }
}
```

### 4.2 Status Effect Types

| Type | Purpose | Example |
|------|---------|---------|
| **DamageOverTime** | DoT (Burn, Poison, Bleed) | Deals damage each turn |
| **HealOverTime** | HoT (Regeneration) | Heals each turn |
| **StatModifier** | Buff/Debuff stats | +30% ATK, -20% DEF |
| **CrowdControl** | Disable actions | Stun, Freeze, Sleep |
| **Shield** | Absorb damage | Barrier with HP pool |
| **Immunity** | Prevent debuffs | Cleanse + immunity |

### 4.3 Common Status Effects

#### Burn (Fire DoT)
- Deals Fire damage each turn
- Stackable (max 5 stacks)
- Duration: 3 turns
- Formula: `(caster.atk * 0.5)` per turn per stack

#### Freeze (Ice CC)
- Disables all actions
- Duration: 1 turn
- Not stackable
- Can be cleansed

#### Poison (Nature DoT)
- Deals Nature damage each turn
- Ignores defense
- Stackable (max 10 stacks)
- Duration: 5 turns

#### Regeneration (Heal HoT)
- Heals each turn
- Duration: 3 turns
- Formula: `(caster.atk * 1.5)` per turn

#### Stun (CC)
- Prevents skill casting
- Allows basic attacks
- Duration: 1 turn

---

## 🎯 **5. Skill Categories**

### 5.1 Basic Attacks
- **Cooldown**: 0 (always available)
- **Cost**: 0 (free)
- **Purpose**: Generate energy/mana
- **Example**: Auto-attack, basic shot

### 5.2 Active Skills
- **Cooldown**: 3-8 turns
- **Cost**: Mana (20-80)
- **Purpose**: Core combat abilities
- **Example**: Fireball, Ice Lance, Heal

### 5.3 Ultimate Skills
- **Cooldown**: 10-20 turns
- **Cost**: Energy (100)
- **Purpose**: High-impact game-changers
- **Example**: Phoenix Strike, Blizzard, Resurrection

### 5.4 Passive Skills
- **Cooldown**: N/A
- **Cost**: 0
- **Purpose**: Always-active bonuses
- **Example**: Fire Mastery (+20% Fire DMG)

---

## 🛠️ **6. Unity Integration**

### 6.1 Loading Skills

```csharp
// Load skill config
var skillConfig = ConfigManager.GetSkill("SKILL_PHOENIX_STRIKE");

// Execute skill
SkillExecutor.Execute(skillConfig, caster, target);
```

### 6.2 Timeline Execution

The SkillExecutor processes the timeline sequentially:

```csharp
foreach (var timelineEntry in skillConfig.timeline) {
    await UniTask.Delay((int)(timelineEntry.time * 1000));

    switch (timelineEntry.action) {
        case "PlayAnim":
            caster.PlayAnimation(timelineEntry.param);
            break;
        case "ApplyEffects":
            ApplyEffectPayload(timelineEntry.payload, caster, target);
            break;
        case "SpawnVFX":
            VFXManager.Spawn(timelineEntry.param, target.position);
            break;
    }
}
```

### 6.3 Formula Evaluation

```csharp
var damage = FormulaEvaluator.Evaluate(
    effectConfig.formula,
    new { caster, target }
);
```

The FormulaEvaluator supports:
- Variable substitution: `caster.atk` → `245`
- Math operations: `(245 * 3.5) + (5000 * 0.05)`
- Functions: `min()`, `max()`, `pow()`, `sqrt()`

---

## 💡 **7. Design Best Practices**

### 7.1 Skill Balance
- **DPS Skills**: ATK multiplier 1.5-4.0x
- **Tank Skills**: DEF multiplier 2.0-5.0x
- **Heal Skills**: ATK multiplier 1.5-3.0x
- **Utility Skills**: No direct damage, focus on debuffs

### 7.2 Timeline Timing
- **Animation Start**: 0.0s
- **VFX Charge**: 0.2-0.3s
- **Damage Apply**: 0.4-0.6s (sync with visual impact)
- **VFX Impact**: Same as damage
- **Camera Shake**: Slightly after damage (0.05s delay)

### 7.3 Cooldown Guidelines
- **Basic**: 0 turns
- **Common Skills**: 3-5 turns
- **Rare Skills**: 6-10 turns
- **Ultimates**: 12-20 turns

### 7.4 Cost Guidelines
- **Basic**: 0
- **Common Skills**: 20-40 Mana
- **Rare Skills**: 50-80 Mana
- **Ultimates**: 100 Energy

---

## 📊 **8. Skill Config Workflow**

### Game Designer Workflow:
1. Define skill concept (damage, element, effect)
2. Add entry to Skills.json
3. Set meta (name, tags)
4. Configure activation (cooldown, cost)
5. Design targeting (mode, range, AoE)
6. Build timeline (anim → VFX → damage → effects)
7. Test in game, iterate

### No Code Changes Required:
- All skill logic is data-driven
- Hot-update capable via CDN
- Server can push balance patches instantly
- Client auto-reloads on config change

---

## ✅ **9. Summary**

The Skills.json system enables:

✅ **100% data-driven combat** - No hardcoded skill logic
✅ **Timeline-based execution** - Frame-perfect synchronization
✅ **Formula-driven damage** - Dynamic calculations with character stats
✅ **Flexible targeting** - Support for single, AoE, self, ally, enemy
✅ **Status effect system** - DoT, HoT, buffs, debuffs, CC
✅ **Addressables integration** - Lazy-load VFX and animations
✅ **Hot-update ready** - Balance patches without client updates

**Use Cases:**
- **New Character**: Add skills to Skills.json, reference in Characters.json
- **Balance Patch**: Adjust formulas, cooldowns, costs
- **New Status**: Add to statusEffects[], reference in skill payloads
- **Visual Update**: Change VFX references, no code change

---

**This configuration is the foundation of the combat system and powers all character abilities in the game.**
