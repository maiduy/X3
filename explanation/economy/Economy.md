
---

# 📘 **Economy.json Configuration Explanation**

This file defines the **Game Economy System** including currencies, resources, exchange rates, and economic balance targets. It serves as the master specification for monitoring and tuning the game's economic health.

---

## 🧩 **1. Structure Overview**

```json
{
  "currencies": [...]  // Gold, Gems, Arena Coins, etc.
  "resources": [...]   // Energy, Stamina, etc.
  "economy Balance": {...}  // Balance targets by player segment
  "conversionRates": {...}  // Currency exchange rates
}
```

---

## 💰 **2. Currency System**

### 2.1 Currency Types

| Type | Examples | Acquisition | Purpose |
|------|----------|-------------|---------|
| **Soft** | Gold | Gameplay (stages, quests, selling) | Character/item upgrades |
| **Premium** | Gems | IAP, rewards | Gacha, premium shop |
| **Event** | Arena Coins | Specific modes (PvP, Raids) | Event shop items |

### 2.2 Currency Configuration

```json
{
  "id": "CURRENCY_GOLD",
  "meta": {...},
  "limits": {...},
  "sources": [...],
  "sinks": [...],
  "dailyEconomy": {...}
}
```

#### Key Fields:

| Field | Purpose |
|-------|---------|
| **sources** | Where currency comes from (avg per day/run) |
| **sinks** | Where currency is spent (avg per day) |
| **dailyEconomy** | Target income/spending/inflation |
| **limits.maxAmount** | Maximum holdings (prevents overflow) |
| **conversionRates** | Exchange rates to other currencies |

### 2.3 Economic Monitoring

**For each currency, track:**
- **Target Income**: How much players should earn daily
- **Target Spending**: How much players should spend daily
- **Inflation Rate**: Expected currency value change over time
- **Sink/Source Ratio**: Balance between earning and spending

**Example (Gold):**
- Target Income: 100,000 gold/day
- Target Spending: 80,000 gold/day
- Net Gain: 20,000 gold/day (allows accumulation)
- Inflation Rate: 2% monthly (planned power creep)

---

## ⚡ **3. Resource System**

### 3.1 Energy (Stamina) System

```json
{
  "id": "RESOURCE_ENERGY",
  "limits": {
    "maxAmount": 200,      // Hard cap
    "naturalCap": 100      // Regeneration stops here
  },
  "regeneration": {
    "rate": 1,             // Amount gained
    "interval": 300,       // Every 5 minutes
    "bonusFromLevel": 0    // No level bonus
  }
}
```

**How it Works:**
- Players start with 100/100 energy
- Regenerates 1 energy per 5 minutes
- **Natural cap**: Regeneration stops at 100
- **Hard cap**: Can store up to 200 (from purchases/gifts)
- Resets daily: No (accumulates indefinitely up to natural cap)

**Usage:**
- Stage runs: Cost 5-20 energy
- Dungeon runs: Cost 10-30 energy
- Can purchase refills with gems

---

## 🔄 **4. Conversion & Exchange**

### 4.1 Currency Conversion

```json
"conversionRates": {
  "toGold": 1000,    // 1 Gem → 1000 Gold
  "toEnergy": 10     // 1 Gem → 10 Energy
}
```

**Gem Exchange Rates:**
- 1 Gem = 1,000 Gold
- 1 Gem = 10 Energy
- Prevents value arbitrage
- Server-authoritative (prevent exploits)

### 4.2 IAP Exchange Rate

```json
"iapExchangeRate": 1.0  // $1 USD = 1.0 Gem base rate
```

**Typical IAP Packages:**
- $0.99 → 100 Gems (bonus included)
- $4.99 → 600 Gems (+20% bonus)
- $29.99 → 4000 Gems (+33% bonus)
- $99.99 → 15000 Gems (+50% bonus)

---

## 📊 **5. Economy Balance by Player Segment**

### 5.1 New Players (Level 1-20)

**Goals:**
- Rapid progression
- Low friction (abundant resources)
- Hook players with generous rewards

**Targets:**
- Gold Income: 50,000/day
- Energy: 150/day (100 natural + 50 bonus)
- Gems: 100/week (from quests)

### 5.2 Mid-Game (Level 21-50)

**Goals:**
- Sustained engagement
- Introduce resource constraints
- Encourage IAP consideration

**Targets:**
- Gold Income: 100,000/day
- Energy: 300/day (need some refills)
- Gems: 200/week

### 5.3 End-Game (Level 51+)

**Goals:**
- Long-term retention
- Premium content access
- Competitive progression

**Targets:**
- Gold Income: 200,000/day
- Energy: 500/day (many refills needed)
- Gems: 500/week (for whales)

---

## 🎯 **6. Sources & Sinks Breakdown**

### 6.1 Gold Sources

| Source | Avg Per Day | Notes |
|--------|-------------|-------|
| Stage Completion | 30,000 | 200 runs × 150 gold |
| Daily Quests | 15,000 | 10 quests × 1,500 |
| Selling Items | 20,000 | Variable (excess gear) |
| Events | 10,000 | Weekend events |
| Arena | 5,000 | Daily participation |
| **Total** | **80,000** | Sustainable income |

### 6.2 Gold Sinks

| Sink | Avg Per Day | Notes |
|------|-------------|-------|
| Character Upgrades | 30,000 | Leveling, ascension |
| Equipment Enhancement | 20,000 | +0 to +15 |
| Shop Purchases | 15,000 | Daily shop refreshes |
| Skill Upgrades | 10,000 | Skill level-ups |
| **Total** | **75,000** | Balanced deficit |

**Balance:**
- Income: 80,000
- Spending: 75,000
- **Net: +5,000/day** (healthy accumulation)

---

## 💎 **7. Premium Currency (Gems)**

### 7.1 Gem Sources

| Source | Avg Per Day | Notes |
|--------|-------------|-------|
| IAP | Variable | Primary source |
| Daily Login | 10 | 7-day streak bonus |
| Quests | 20 | Daily/weekly quests |
| Arena | 15 | Win rewards |
| Events | 30 | Weekend/seasonal |
| **Total (F2P)** | **75/day** | ~525 gems/week |

### 7.2 Gem Sinks

| Sink | Avg Per Day | Notes |
|------|-------------|-------|
| Gacha Pulls | 320 | Single pull = 160 gems |
| Energy Refills | 50 | 50 gems per refill |
| Shop | 100 | Premium items |
| Inventory Expansion | 20 | One-time purchases |
| **Total** | **490/day** | Heavy spenders |

**F2P Balance:**
- F2P Income: 75/day
- Conservative Spending: 160/day (1 gacha pull)
- **F2P can pull ~3 times/week** (sustainable)

---

## 🔬 **8. Economic Monitoring Metrics**

### 8.1 Key Metrics to Track

| Metric | Purpose | Target |
|--------|---------|--------|
| **Sink/Source Ratio** | Currency balance | 0.8-0.95 (slight surplus) |
| **Gem Velocity** | How fast gems are spent | <2 days (healthy spending) |
| **Gold Inflation** | Currency value over time | 2-3% monthly |
| **Energy Utilization** | Are players capped? | 70-85% used daily |
| **Conversion Rate** | F2P → Paying | 2-5% industry standard |

### 8.2 Warning Signs

🚨 **Inflation Too High (>5%/month)**
- Players hoarding, not spending
- Sinks too weak
- **Fix**: Introduce desirable sinks, increase costs

🚨 **Deflation (<0%)**
- Players running out of currency
- Sources too weak
- **Fix**: Increase rewards, reduce costs

🚨 **Energy Always Capped**
- Players not engaging
- **Fix**: More energy sinks, better rewards

---

## 🛠️ **9. Unity Integration**

### Currency Operations

```csharp
// Award currency
CurrencyManager.Add("CURRENCY_GOLD", 1000);

// Deduct currency
if (CurrencyManager.Has("CURRENCY_GOLD", 500)) {
    CurrencyManager.Remove("CURRENCY_GOLD", 500);
}

// Check balance
int goldBalance = CurrencyManager.GetBalance("CURRENCY_GOLD");

// Convert currency
CurrencyManager.Convert("CURRENCY_GEM", 10, "CURRENCY_GOLD");
// Converts 10 gems → 10,000 gold
```

### Energy System

```csharp
// Check energy
if (ResourceManager.Has("RESOURCE_ENERGY", 10)) {
    ResourceManager.Consume("RESOURCE_ENERGY", 10);
    StartStage();
}

// Refill energy
ResourceManager.Add("RESOURCE_ENERGY", 50); // From gem purchase

// Check regeneration
float timeToFull = ResourceManager.GetTimeToFullEnergy();
```

---

## 💡 **10. Design Best Practices**

### 10.1 Soft Currency (Gold)
- **Abundant**: Players should never feel starved
- **Many Sources**: Stages, quests, selling, events
- **Gradual Sinks**: Constant spending, not all-at-once
- **Target**: Slight daily surplus (5-10%)

### 10.2 Premium Currency (Gems)
- **Scarce**: Valuable, earned slowly
- **IAP Primary**: F2P can earn, but limited
- **Desirable Sinks**: Gacha, exclusive items
- **Target**: F2P sustainable at ~1 pull/2 days

### 10.3 Event Currency
- **Time-Limited**: Earned during events only
- **Exclusive Shop**: Special items not available elsewhere
- **No Carry-Over**: Encourages spending during event
- **Target**: Enough for 1-2 premium items per event

### 10.4 Energy/Stamina
- **Natural Regen**: 1 per 5 minutes (288/day)
- **Gem Refills**: 50 gems for 50 energy
- **Cap Management**: Natural cap 100, hard cap 200
- **Target**: Players should refill 1-2 times/day (mid/end game)

---

## ✅ **11. Summary**

The Economy.json system provides:

✅ **Multi-currency system** - Soft, premium, event currencies
✅ **Regenerating resources** - Energy/stamina mechanics
✅ **Economic tracking** - Sources, sinks, daily targets
✅ **Exchange rates** - Gem to gold/energy conversion
✅ **Player segmentation** - Different targets by progress level
✅ **Inflation monitoring** - Track currency value over time
✅ **Server-authoritative** - Prevent exploits and hacks

**Use Cases:**
- **Balance Patch**: Adjust sources/sinks to fix inflation
- **New Currency**: Add event currency for limited-time mode
- **Conversion Rates**: Tune gem value based on economy health
- **IAP Pricing**: Set exchange rates for monetization

---

**This configuration is the backbone of the game's economic health and player progression pacing.**
