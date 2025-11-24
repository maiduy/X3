
---

# 📘 **BattleRules.json Configuration Explanation**

This file defines the **Core Combat System Rules** for the game. It governs turn order, damage calculation, elemental interactions, resource management, and victory/defeat conditions. This configuration ensures consistent combat behavior across PvE, PvP, and all game modes.

---

## 🧩 **1. Overall Structure**

```json
{
  "$schema": "...",
  "version": "1.0.0",
  "lastUpdated": "2025-11-23T00:00:00Z",
  "turnSystem": {...},
  "combatPhases": {...},
  "elementalSystem": {...},
  "damageCalculation": {...},
  "resourceSystem": {...},
  "combatRules": {...},
  "crowdControlRules": {...},
  "teamComposition": {...},
  "victoryConditions": [...],
  "defeatConditions": [...],
  "rewardCalculation": {...}
}
```

---

## ⚔️ **2. Turn System**

```json
"turnSystem": {
  "type": "ATB",
  "baseTickRate": 100,
  "speedToATBMultiplier": 1.0,
  "actionCost": 100,
  "turnOrderDisplay": true
}
```

### ATB (Active Time Battle) System

| Field | Meaning |
|-------|---------|
| **type** | Turn system type: ATB, CTB (Classic Turn-Based), Real-Time |
| **baseTickRate** | Units filled per game tick |
| **speedToATBMultiplier** | How SPD stat affects ATB fill rate |
| **actionCost** | ATB cost to take an action (100 = full bar) |
| **turnOrderDisplay** | Show turn order UI to player |

**How ATB Works:**
1. All units start with 0 ATB
2. Each game tick, units gain ATB = `baseTickRate * (unit.spd / 100) * speedToATBMultiplier`
3. When ATB reaches 100, unit can act
4. After acting, ATB resets to 0

**Example:**
- Unit with SPD 120: Gains 120 ATB per tick → Acts every 0.83 ticks
- Unit with SPD 80: Gains 80 ATB per tick → Acts every 1.25 ticks

---

## 🎬 **3. Combat Phases**

```json
"combatPhases": {
  "preparation": {
    "duration": 3.0,
    "allowSkillSelection": true,
    "allowPositioning": false
  },
  "combat": {
    "maxTurns": 50,
    "turnTimeout": 30.0,
    "autoPlayEnabled": true
  },
  "victory": {
    "duration": 2.0,
    "showRewards": true
  },
  "defeat": {
    "duration": 2.0,
    "allowRetry": true
  }
}
```

### Phase Breakdown:

| Phase | Duration | Purpose |
|-------|----------|---------|
| **Preparation** | 3.0s | Players select skills, review enemy team |
| **Combat** | Until win/loss | Main battle phase with turn limit |
| **Victory** | 2.0s | Victory animation, reward display |
| **Defeat** | 2.0s | Defeat animation, retry option |

**Combat Phase Settings:**
- **maxTurns**: Battle ends in draw after 50 turns
- **turnTimeout**: Player has 30 seconds to act (auto-play kicks in)
- **autoPlayEnabled**: AI takes over if timeout

---

## 🔥 **4. Elemental System**

```json
"elementalSystem": {
  "elements": ["Fire", "Ice", "Nature", "Light", "Dark", "Physical"],
  "advantageMatrix": {
    "Fire": {"strong": ["Nature", "Ice"], "weak": ["Ice", "Water"]},
    "Ice": {"strong": ["Nature", "Fire"], "weak": ["Fire"]},
    ...
  },
  "advantageMultiplier": {
    "strong": 1.5,
    "neutral": 1.0,
    "weak": 0.75
  }
}
```

### Elemental Advantage Matrix:

| Attacker → Defender | Fire | Ice | Nature | Light | Dark | Physical |
|---------------------|------|-----|--------|-------|------|----------|
| **Fire** | 1.0 | **0.75** | **1.5** | 1.0 | 1.0 | 1.0 |
| **Ice** | **1.5** | 1.0 | **1.5** | 1.0 | 1.0 | 1.0 |
| **Nature** | **0.75** | **0.75** | 1.0 | 1.0 | 1.0 | 1.0 |
| **Light** | 1.0 | 1.0 | 1.0 | 1.0 | **1.5** | 1.0 |
| **Dark** | 1.0 | 1.0 | 1.0 | **1.5** | 1.0 | 1.0 |
| **Physical** | 1.0 | 1.0 | 1.0 | 1.0 | 1.0 | 1.0 |

**Multipliers:**
- **Strong Advantage**: 1.5x damage (150%)
- **Neutral**: 1.0x damage (100%)
- **Weak/Disadvantage**: 0.75x damage (75%)

**Strategy:**
- Fire beats Nature (burn vegetation)
- Ice beats Fire (extinguish flames)
- Light and Dark counter each other
- Physical has no advantage/disadvantage (stable)

---

## 💥 **5. Damage Calculation**

### 5.1 Damage Formulas

```json
"damageCalculation": {
  "physicalFormula": "(atk * skillMultiplier * (1 + critDmg * isCrit)) * (100 / (100 + def)) * elementalMultiplier * randomFactor",
  "magicalFormula": "(atk * skillMultiplier * (1 + critDmg * isCrit)) * (100 / (100 + def * 0.5)) * elementalMultiplier * randomFactor",
  "trueDamageFormula": "atk * skillMultiplier"
}
```

### Physical Damage Formula Breakdown:

**Step 1: Base Damage**
```
baseDamage = atk * skillMultiplier
```
- Example: 200 ATK × 3.5 multiplier = 700 base damage

**Step 2: Critical Hit**
```
damage = baseDamage * (1 + critDmg * isCrit)
```
- If crit (isCrit=1): 700 × (1 + 1.5 × 1) = 700 × 2.5 = 1750 damage
- If no crit (isCrit=0): 700 × 1 = 700 damage

**Step 3: Defense Reduction**
```
damage = damage * (100 / (100 + def))
```
- vs 150 DEF: 1750 × (100 / 250) = 1750 × 0.4 = 700 damage
- vs 0 DEF: 1750 × (100 / 100) = 1750 damage

**Step 4: Elemental Modifier**
```
damage = damage * elementalMultiplier
```
- Strong: 700 × 1.5 = 1050 damage
- Weak: 700 × 0.75 = 525 damage

**Step 5: Random Factor**
```
damage = damage * random(0.95, 1.05)
```
- Adds ±5% variance to prevent repetitive combat

### Magical Damage:
- Same formula, but DEF is halved: `def * 0.5`
- Magical attacks penetrate defense better
- Example: vs 150 DEF, magical uses 75 effective DEF

### True Damage:
- **Ignores defense completely**
- **No elemental modifier**
- Formula: `atk * skillMultiplier`
- Used for execute abilities, burn DoT

---

### 5.2 Critical Hit System

```json
"criticalHit": {
  "baseCritRate": 0.05,
  "baseCritDmg": 1.5,
  "critRateCap": 1.0,
  "critDmgCap": 3.0
}
```

| Field | Meaning |
|-------|---------|
| **baseCritRate** | All units start with 5% crit chance |
| **baseCritDmg** | Crits deal 150% bonus damage (×2.5 total) |
| **critRateCap** | Maximum 100% crit rate (always crit) |
| **critDmgCap** | Maximum 300% bonus (×4.0 total damage) |

**Crit Calculation:**
- Final Crit Rate = `baseCritRate + unit.crit + buffs`
- Capped at 100%
- Final Crit Damage = `baseCritDmg + unit.critDmg + buffs`
- Capped at 300%

---

### 5.3 Defense Formulas

```json
"defenseFormulas": {
  "damageReduction": "def / (def + 100)",
  "defenseIgnore": "max(0, def - (def * ignoreValue))"
}
```

**Damage Reduction:**
- 0 DEF: 0% reduction
- 100 DEF: 50% reduction
- 200 DEF: 66.7% reduction
- 400 DEF: 80% reduction
- ∞ DEF: Approaches 100% (never quite reaches)

**Defense Ignore/Penetration:**
- If skill has 30% DEF ignore: `effectiveDef = def * (1 - 0.3)`
- Example: 200 DEF → 140 effective DEF

---

## ⚡ **6. Resource System**

### 6.1 Mana

```json
"mana": {
  "startAmount": 100,
  "maxAmount": 100,
  "regenPerTurn": 20,
  "regenOnHit": 5,
  "regenOnKill": 30
}
```

**Mana Usage:**
- Starts battle at 100 (full)
- Regenerates 20 per turn
- Gains 5 when hitting enemy
- Gains 30 when killing enemy
- Used for: Skills (cost 20-80)

### 6.2 Energy (Ultimate Gauge)

```json
"energy": {
  "startAmount": 0,
  "maxAmount": 100,
  "gainOnAttack": 10,
  "gainOnHit": 5,
  "gainOnKill": 20,
  "decayPerTurn": 0
}
```

**Energy Usage:**
- Starts battle at 0 (empty)
- Gains 10 when attacking
- Gains 5 when taking damage
- Gains 20 when killing enemy
- No decay (persists across turns)
- Used for: Ultimate skills (cost 100)

**Strategy:**
- Tanks gain energy fast (take many hits)
- DPS gain energy from kills
- Support gain slowly (fewer attacks/hits)

---

## 🎯 **7. Combat Rules**

```json
"combatRules": {
  "allowFriendlyFire": false,
  "allowSelfTarget": true,
  "resurrectAllowed": true,
  "maxRevivesPerBattle": 3,
  "speedTiebreakerRandom": true,
  "continuousEffectTiming": "EndOfTurn"
}
```

| Rule | Effect |
|------|--------|
| **allowFriendlyFire** | AoE skills cannot damage allies |
| **allowSelfTarget** | Can target self with buffs/heals |
| **resurrectAllowed** | Dead units can be revived |
| **maxRevivesPerBattle** | Each unit can revive 3 times max |
| **speedTiebreakerRandom** | If SPD equal, random turn order |
| **continuousEffectTiming** | DoT/HoT apply at end of turn |

---

## 🎭 **8. Crowd Control Rules**

```json
"crowdControlRules": {
  "stunPreventsAction": true,
  "freezePreventsAction": true,
  "sleepBreaksOnDamage": true,
  "charmChangesTeam": true,
  "maxCCDuration": 5,
  "ccResistanceStacking": "Multiplicative"
}
```

### CC Effects:

| CC Type | Prevents Actions | Breaks on Damage | Duration Cap |
|---------|------------------|------------------|--------------|
| **Stun** | Yes | No | 5 turns |
| **Freeze** | Yes | No | 5 turns |
| **Sleep** | Yes | Yes | 5 turns |
| **Charm** | No (switches team) | No | 5 turns |

**CC Resistance Stacking:**
- **Multiplicative**: (1 - res1) × (1 - res2)
- Example: 30% + 30% resist = 1 - (0.7 × 0.7) = 51% total
- Not additive (prevents 100% immunity from stacking)

---

## 👥 **9. Team Composition**

```json
"teamComposition": {
  "pvp": {
    "minUnits": 3,
    "maxUnits": 5,
    "duplicatesAllowed": false,
    "sameElementLimit": null
  },
  "pve": {
    "minUnits": 1,
    "maxUnits": 5,
    "duplicatesAllowed": false,
    "sameElementLimit": null
  }
}
```

### PvP Rules:
- **3-5 units** per team
- **No duplicate characters** (each character once)
- **No element limit** (can use 5 Fire units)

### PvE Rules:
- **1-5 units** (allows solo challenge runs)
- **No duplicate characters**
- **No element limit**

---

## 🏆 **10. Victory Conditions**

```json
"victoryConditions": [
  {
    "id": "ELIMINATE_ALL",
    "type": "EliminateEnemies",
    "description": "Defeat all enemy units"
  },
  {
    "id": "SURVIVE_TURNS",
    "type": "SurviveDuration",
    "param": {"turns": 10}
  },
  {
    "id": "PROTECT_TARGET",
    "type": "KeepAlive",
    "param": {"targetId": "PROTECTED_UNIT"}
  },
  {
    "id": "KILL_BOSS",
    "type": "EliminateSpecific",
    "param": {"targetTag": "Boss"}
  }
]
```

### Victory Types:

| Type | Condition | Use Case |
|------|-----------|----------|
| **ELIMINATE_ALL** | Kill all enemies | Standard battles |
| **SURVIVE_TURNS** | Survive N turns | Tower defense, survival mode |
| **PROTECT_TARGET** | Keep NPC alive | Escort missions |
| **KILL_BOSS** | Defeat boss unit | Boss stages, raids |

---

## 💀 **11. Defeat Conditions**

```json
"defeatConditions": [
  {
    "id": "ALL_DEAD",
    "type": "AllUnitsDefeated"
  },
  {
    "id": "TIMEOUT",
    "type": "TurnLimit",
    "param": {"maxTurns": 50}
  },
  {
    "id": "VIP_DEAD",
    "type": "ProtectedUnitDied",
    "param": {"targetId": "VIP_UNIT"}
  }
]
```

### Defeat Types:

| Type | Condition | Use Case |
|------|-----------|----------|
| **ALL_DEAD** | All player units dead | Standard battles |
| **TIMEOUT** | Exceeded turn limit | Prevent infinite battles |
| **VIP_DEAD** | Protected unit died | Escort missions |

---

## 🎁 **12. Reward Calculation**

```json
"rewardCalculation": {
  "baseExpFormula": "enemyLevel * 10 + enemyRarity * 50",
  "baseGoldFormula": "enemyLevel * 5 + enemyRarity * 25",
  "performanceMultipliers": {
    "noDeaths": 1.5,
    "fastClear": 1.3,
    "perfectHealth": 1.2
  },
  "firstClearBonus": 2.0
}
```

### Reward Formulas:

**Base EXP:**
```
exp = enemyLevel * 10 + enemyRarity * 50
```
- Level 20 SR enemy: (20 × 10) + (2 × 50) = 300 EXP

**Base Gold:**
```
gold = enemyLevel * 5 + enemyRarity * 25
```
- Level 20 SR enemy: (20 × 5) + (2 × 25) = 150 Gold

**Performance Bonuses:**
- **No Deaths**: +50% rewards
- **Fast Clear**: +30% rewards (clear under time/turn threshold)
- **Perfect Health**: +20% rewards (end with >90% HP)

**First Clear Bonus:**
- **2.0x multiplier** on first stage completion
- Encourages exploration of new content

**Example:**
- Base: 300 EXP + 150 Gold
- No Deaths (1.5x): 450 EXP + 225 Gold
- Fast Clear (1.3x): 585 EXP + 292 Gold
- First Clear (2.0x): 1170 EXP + 584 Gold

---

## 🛠️ **13. Unity Integration**

### Loading Rules

```csharp
// Load battle rules
var rules = ConfigManager.GetBattleRules();

// Apply ATB system
battleSystem.SetTurnSystem(rules.turnSystem);

// Apply elemental matrix
elementalSystem.LoadAdvantageMatrix(rules.elementalSystem);

// Apply damage formulas
damageCalculator.SetFormulas(rules.damageCalculation);
```

### Damage Calculation Example

```csharp
float CalculateDamage(Unit attacker, Unit defender, Skill skill) {
    var rules = ConfigManager.GetBattleRules();

    // Base damage
    float damage = attacker.atk * skill.multiplier;

    // Crit
    bool isCrit = Random.value < attacker.critRate;
    if (isCrit) {
        damage *= (1 + attacker.critDmg);
    }

    // Defense
    damage *= (100f / (100f + defender.def));

    // Elemental
    float elementalMult = GetElementalMultiplier(
        skill.element,
        defender.element,
        rules.elementalSystem
    );
    damage *= elementalMult;

    // Random
    damage *= Random.Range(
        rules.damageCalculation.randomFactor.min,
        rules.damageCalculation.randomFactor.max
    );

    return damage;
}
```

---

## 💡 **14. Design Best Practices**

### 14.1 Balancing Guidelines
- **Tank HP**: 3-5x DPS HP
- **DPS Damage**: 2-3x Tank damage
- **Healer Output**: 1.5-2x DPS damage as healing
- **Speed**: DPS 100-120, Tank 80-100, Healer 90-110

### 14.2 Turn Limit Guidelines
- **Normal Stage**: 30-40 turns
- **Boss Stage**: 40-50 turns
- **Raid**: 100+ turns
- **PvP**: 20-30 turns

### 14.3 Elemental Balance
- Each element should have 2 advantages, 2 disadvantages
- Physical = neutral (stable for beginners)
- Light/Dark counter each other (PvP meta)

---

## ✅ **15. Summary**

The BattleRules.json system enables:

✅ **Consistent combat** - Same rules across all game modes
✅ **ATB turn system** - Speed-based action ordering
✅ **Elemental strategy** - Rock-paper-scissors counters
✅ **Formula-driven damage** - Predictable, balanced calculations
✅ **Resource management** - Mana/Energy systems
✅ **Flexible victory** - Multiple win/loss conditions
✅ **Performance rewards** - Incentivize skillful play

**Use Cases:**
- **New Game Mode**: Reuse same rules, change victory condition
- **Balance Patch**: Adjust multipliers, formulas, caps
- **PvP Tuning**: Modify team composition limits
- **Event Mode**: Override turn limits, resource regen

---

**This configuration is the foundation of all combat encounters and ensures balanced, strategic gameplay.**
