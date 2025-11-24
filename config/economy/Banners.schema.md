# Banners.json Schema Documentation

## Overview
Defines gacha/summoning banner configurations including rates, pity systems, character pools, pickup mechanics, guarantees, and bonuses. Controls the gacha economy and character acquisition system.

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

### `banners` (array)
- **Type**: Array of Banner Objects
- **Description**: All gacha banner configurations
- **Required**: Yes

### `dropRateDisplay` (object)
- **Description**: Drop rate display and legal compliance settings
- **Required**: Yes

### `wishSystem` (object)
- **Description**: Wish/selector system configuration
- **Required**: Yes

---

## Banner Object

### `id` (string)
- **Format**: `BANNER_[NAME]`
- **Example**: `"BANNER_STANDARD"`, `"BANNER_FIRE_DRAGON_V1"`, `"BANNER_BEGINNER"`
- **Description**: Unique banner identifier
- **Required**: Yes

### `meta` (object)
Banner metadata and display information.

#### `nameKey` (string)
- **Type**: Localization Key
- **Description**: Banner name localization key
- **Format**: `BANNER_NAME_[BANNER_ID]`
- **Example**: `"BANNER_NAME_STANDARD"`

#### `descriptionKey` (string)
- **Type**: Localization Key
- **Description**: Banner description localization key
- **Format**: `BANNER_DESC_[BANNER_ID]`
- **Example**: `"BANNER_DESC_STANDARD"`

#### `icon` (string)
- **Type**: Asset Reference
- **Description**: Banner icon asset path
- **Example**: `"Icon_Banner_Standard"`

#### `type` (string)
- **Values**: `"Permanent"`, `"Limited"`, `"Beginner"`, `"Weapon"`, `"Event"`
- **Description**: Banner category type
- **Required**: Yes

**Banner Types**:
- **Permanent**: Always available, standard pool
- **Limited**: Time-limited, featured characters
- **Beginner**: New player exclusive banner
- **Weapon**: Equipment/weapon gacha
- **Event**: Special event banners

#### `isPermanent` (boolean)
- **Description**: Whether banner is permanently available
- **Default**: `false`
- **True**: Always available
- **False**: Time-limited

#### `featuredCharacter` (string)
- **Type**: Character ID
- **Description**: Featured/pickup character
- **Example**: `"CHAR_FIRE_DRAGON"`
- **Optional**: Only for limited banners with pickups

---

## Duration Object

### `start` (string)
- **Type**: ISO 8601 DateTime
- **Description**: Banner start time
- **Example**: `"2025-11-01T00:00:00Z"`
- **Required**: Yes

### `end` (string | null)
- **Type**: ISO 8601 DateTime or null
- **Description**: Banner end time
- **Example**: `"2025-11-15T23:59:59Z"`
- **Null**: Permanent banner (no end)

### `expiresAfterDays` (number)
- **Type**: Integer (days)
- **Description**: Expires X days after player first sees it
- **Example**: `7` = expires 7 days after first login
- **Optional**: Only for beginner banners
- **Usage**: Per-player expiration

### `expiresAfterPulls` (number)
- **Type**: Integer (pulls)
- **Description**: Expires after X pulls
- **Example**: `20` = max 20 pulls then disappears
- **Optional**: Only for beginner banners

---

## Cost Object

### `single` (array)
- **Type**: Array of Cost Items
- **Description**: Single pull cost options (player can choose)
- **Structure**: `[{"itemId": "...", "amount": number}]`
- **Example**:
```json
[
  {"itemId": "ITEM_SUMMON_SCROLL", "amount": 1},
  {"itemId": "CURRENCY_GEM", "amount": 160}
]
```
**Usage**: Player can use 1 scroll OR 160 gems

### `multi` (array)
- **Type**: Array of Cost Items
- **Description**: Multi-pull cost options (10-pull)
- **Example**:
```json
[
  {"itemId": "ITEM_SUMMON_SCROLL", "amount": 10},
  {"itemId": "CURRENCY_GEM", "amount": 1600}
]
```
**Usage**: 10-pull = 10 scrolls OR 1600 gems

---

## Pity Object

### `soft` (number | null)
- **Type**: Integer (pulls) or null
- **Description**: Soft pity starts increasing rates
- **Example**: `70` = rates increase after 70 pulls
- **Null**: No soft pity
- **Usage**: Gradual rate increase until hard pity

### `hard` (number)
- **Type**: Integer (pulls)
- **Description**: Guaranteed SSR at this pull count
- **Example**: `90` = guaranteed SSR at 90th pull
- **Required**: Yes
- **Usage**: Maximum pulls before guaranteed SSR

### `resetOnSSR` (boolean)
- **Description**: Reset pity counter when SSR obtained
- **Default**: `true`
- **True**: Counter resets to 0 on SSR
- **False**: Counter persists

### `carryOver` (boolean)
- **Description**: Carry pity to next banner of same type
- **Default**: `false` (limited), `true` (permanent)
- **True**: Pity carries between banners
- **False**: Pity resets when banner ends

### `guaranteedPickupOn` (number | null)
- **Type**: Integer or null
- **Description**: Which SSR is guaranteed pickup
- **Example**: `2` = 2nd SSR guaranteed pickup
- **Null**: No pickup guarantee
- **Usage**: 50/50 system implementation

**Example**: `guaranteedPickupOn: 2`
- 1st SSR: 50/50 (may lose)
- 2nd SSR: 100% guaranteed pickup

---

## Rates Object

### `SSR` (number)
- **Type**: Float (0.0-1.0)
- **Description**: SSR drop rate
- **Example**: `0.02` = 2%
- **Required**: Yes
- **Standard**: 0.01-0.03 (1-3%)

### `SR` (number)
- **Type**: Float (0.0-1.0)
- **Description**: SR drop rate
- **Example**: `0.18` = 18%
- **Required**: Yes
- **Standard**: 0.15-0.25 (15-25%)

### `R` (number)
- **Type**: Float (0.0-1.0)
- **Description**: R (common) drop rate
- **Example**: `0.80` = 80%
- **Required**: Yes
- **Standard**: 0.70-0.84 (70-84%)

**Validation**: `SSR + SR + R = 1.0` (100%)

---

## Pool Object

### `SSR` (array)
Array of SSR characters/items in pool.

#### Pool Item Object

##### `id` (string)
- **Type**: Character/Item ID
- **Description**: Item identifier
- **Example**: `"CHAR_FIRE_DRAGON"`
- **Required**: Yes

##### `weight` (number)
- **Type**: Float
- **Description**: Relative probability weight
- **Example**: `0.5` = 50% share of SSR pool
- **Required**: Yes
- **Usage**: Weight / Total Weights = individual rate

**Weight Calculation**:
```
SSR Rate: 2%
Pool:
- Fire Dragon: weight 0.5
- Holy Knight: weight 0.5
Total Weight: 1.0

Fire Dragon rate: 2% × (0.5 / 1.0) = 1%
Holy Knight rate: 2% × (0.5 / 1.0) = 1%
```

##### `isPickup` (boolean)
- **Description**: Whether this is the featured/pickup item
- **Default**: `false`
- **True**: Featured item with higher rate
- **False**: Standard pool item

### `SR` (array)
- **Structure**: Same as SSR pool
- **Description**: SR rarity pool

### `R` (array)
- **Structure**: Same as SSR pool
- **Description**: R (common) rarity pool

---

## Pickup Object

### `characterId` (string)
- **Type**: Character ID
- **Description**: Pickup/featured character
- **Example**: `"CHAR_FIRE_DRAGON"`
- **Required**: For limited banners

### `rateShare` (number)
- **Type**: Float (0.0-1.0)
- **Description**: Share of SSR rate for pickup
- **Example**: `0.5` = 50% of SSR pulls are pickup
- **Usage**: 50/50 system

**Example**:
```
SSR rate: 2%
rateShare: 0.5

Pickup rate: 2% × 0.5 = 1%
Off-banner rate: 2% × 0.5 = 1%
```

### `guaranteedOn` (array)
- **Type**: Array of Integers
- **Description**: Which SSRs are guaranteed pickup
- **Example**: `[1, 2]` = 1st and 2nd SSR guaranteed pickup
- **Usage**: Override 50/50 on specific SSRs

---

## Guarantees Object

### Multi-Pull Guarantee
```json
{
  "multiPullGuarantee": {
    "enabled": true,
    "minRarity": "SR",
    "pullCount": 10
  }
}
```

#### `enabled` (boolean)
- **Description**: Enable multi-pull guarantee
- **Default**: `true`

#### `minRarity` (string)
- **Values**: `"SR"`, `"SSR"`
- **Description**: Minimum guaranteed rarity
- **Default**: `"SR"`
- **Usage**: At least 1 SR in 10-pull

#### `pullCount` (number)
- **Type**: Integer
- **Description**: Number of pulls for guarantee
- **Default**: `10`

### Pickup Guarantee
```json
{
  "pickupGuarantee": {
    "enabled": true,
    "maxNonPickupSSR": 1,
    "description": "If you lose 50/50 once, next SSR is guaranteed pickup"
  }
}
```

#### `maxNonPickupSSR` (number)
- **Type**: Integer
- **Description**: Max non-pickup SSRs before guaranteed pickup
- **Example**: `1` = lose 50/50 once, then guaranteed pickup
- **Usage**: Implement 50/50 system

#### `description` (string)
- **Type**: Display Text
- **Description**: Explanation for players
- **Example**: `"If you lose 50/50 once, next SSR is guaranteed pickup"`

### First Ten Pull Guarantee
```json
{
  "firstTenPull": {
    "enabled": true,
    "guaranteedSSR": true,
    "guaranteedCount": 1
  }
}
```

#### `guaranteedSSR` (boolean)
- **Description**: Guarantee SSR in first 10-pull
- **Default**: `false`
- **Usage**: Beginner banner feature

#### `guaranteedCount` (number)
- **Type**: Integer
- **Description**: Number of guaranteed SSRs
- **Example**: `1` = 1 SSR in first 10-pull

---

## Bonuses Object

### First Pull Bonus
```json
{
  "firstPull": {
    "enabled": true,
    "discount": 0.5
  }
}
```

#### `discount` (number)
- **Type**: Float (0.0-1.0)
- **Description**: Discount percentage
- **Example**: `0.5` = 50% off first pull
- **Usage**: Encourage first pull

### Daily Discount
```json
{
  "dailyDiscount": {
    "enabled": true,
    "discountedPulls": 1,
    "discount": 0.2
  }
}
```

#### `discountedPulls` (number)
- **Type**: Integer
- **Description**: Number of discounted pulls per day
- **Example**: `1` = 1 discounted pull per day

#### `discount` (number)
- **Type**: Float (0.0-1.0)
- **Description**: Discount percentage
- **Example**: `0.2` = 20% off

### General Discount
```json
{
  "discount": {
    "enabled": true,
    "discount": 0.25
  }
}
```

#### `discount` (number)
- **Type**: Float (0.0-1.0)
- **Description**: Flat discount on all pulls
- **Example**: `0.25` = 25% off (beginner banner)

---

## Drop Rate Display Object

### `showIndividualRates` (boolean)
- **Description**: Show individual character rates
- **Default**: `true`
- **Usage**: Display "Fire Dragon: 1%" etc.

### `showCumulativeRates` (boolean)
- **Description**: Show cumulative probability
- **Default**: `true`
- **Usage**: "90% chance of SSR within 90 pulls"

### `includHistory` (boolean)
- **Description**: Show player's pull history
- **Default**: `true`
- **Usage**: Personal pull log

### `legalCompliance` (object)
Regional legal requirements.

#### `china` (boolean)
- **Description**: Comply with China gacha laws
- **Default**: `true`
- **Required**: Must show all rates

#### `japan` (boolean)
- **Description**: Comply with Japan gacha laws
- **Default**: `true`

#### `korea` (boolean)
- **Description**: Comply with Korea gacha laws
- **Default**: `true`

---

## Wish System Object

### `enabled` (boolean)
- **Description**: Enable wish/selector system
- **Default**: `false`
- **Usage**: Choose specific SSR to boost rates

### `maxWishes` (number)
- **Type**: Integer
- **Description**: Maximum characters that can be wished for
- **Example**: `5` = select up to 5 characters

### `wishPool` (array)
- **Type**: Array of Character IDs
- **Description**: Available characters for wishing
- **Example**: `["CHAR_FIRE_DRAGON", "CHAR_HOLY_KNIGHT"]`

---

## Banner Type Examples

### Standard Banner
```json
{
  "id": "BANNER_STANDARD",
  "meta": {
    "type": "Permanent",
    "isPermanent": true
  },
  "duration": {
    "start": "2025-01-01T00:00:00Z",
    "end": null
  },
  "cost": {
    "single": [
      {"itemId": "ITEM_SUMMON_SCROLL", "amount": 1},
      {"itemId": "CURRENCY_GEM", "amount": 160}
    ]
  },
  "pity": {
    "soft": 70,
    "hard": 90,
    "resetOnSSR": true,
    "carryOver": true
  },
  "rates": {
    "SSR": 0.02,
    "SR": 0.18,
    "R": 0.80
  }
}
```

**Characteristics**:
- Always available
- Standard 2% SSR rate
- 90 hard pity, 70 soft pity
- Pity carries between pulls
- No pickup system

### Limited Banner (50/50)
```json
{
  "id": "BANNER_FIRE_DRAGON_V1",
  "meta": {
    "type": "Limited",
    "isPermanent": false,
    "featuredCharacter": "CHAR_FIRE_DRAGON"
  },
  "duration": {
    "start": "2025-11-01T00:00:00Z",
    "end": "2025-11-15T23:59:59Z"
  },
  "pity": {
    "soft": 50,
    "hard": 90,
    "carryOver": false,
    "guaranteedPickupOn": 2
  },
  "pickup": {
    "characterId": "CHAR_FIRE_DRAGON",
    "rateShare": 0.5,
    "guaranteedOn": [1, 2]
  },
  "guarantees": {
    "pickupGuarantee": {
      "enabled": true,
      "maxNonPickupSSR": 1
    }
  }
}
```

**Characteristics**:
- Time-limited (2 weeks)
- 50/50 system (1st SSR: 50% pickup, 2nd SSR: 100% pickup)
- Earlier soft pity (50 vs 70)
- Pity doesn't carry (banner-specific)
- Daily discount available

### Beginner Banner
```json
{
  "id": "BANNER_BEGINNER",
  "meta": {
    "type": "Beginner",
    "isPermanent": false
  },
  "duration": {
    "start": "2025-01-01T00:00:00Z",
    "end": null,
    "expiresAfterDays": 7,
    "expiresAfterPulls": 20
  },
  "cost": {
    "single": [
      {"itemId": "CURRENCY_GEM", "amount": 120}
    ]
  },
  "pity": {
    "hard": 10,
    "guaranteedPickupOn": 1
  },
  "rates": {
    "SSR": 0.05,
    "SR": 0.25,
    "R": 0.70
  },
  "guarantees": {
    "firstTenPull": {
      "enabled": true,
      "guaranteedSSR": true
    }
  },
  "bonuses": {
    "discount": {
      "enabled": true,
      "discount": 0.25
    }
  },
  "maxPulls": 20
}
```

**Characteristics**:
- New player only
- Expires after 7 days OR 20 pulls
- 25% discount (120 gems vs 160)
- 5% SSR rate (2.5× standard)
- Guaranteed SSR within 10 pulls
- Maximum 20 pulls total

---

## Pity System Examples

### Standard Pity (90 Hard, 70 Soft)
```
Pull 1-69: Base 2% SSR rate
Pull 70: Rate increases to 3%
Pull 71: Rate increases to 4%
Pull 72: Rate increases to 5%
...
Pull 89: Rate increases to ~20%
Pull 90: 100% guaranteed SSR

Calculation:
Soft pity increase per pull: +1% (configurable)
Pull 70: 2% + 1% = 3%
Pull 71: 3% + 1% = 4%
...continues until hard pity
```

### Limited Banner Pity (50 Soft, 90 Hard)
```
Pull 1-49: Base 2% SSR rate
Pull 50: Rate increases to 4%
Pull 51: Rate increases to 6%
Pull 52: Rate increases to 8%
...
Pull 90: 100% guaranteed SSR

Earlier soft pity = faster rate increase
More player-friendly than standard banner
```

### Beginner Banner Pity (10 Hard)
```
Pull 1-9: 5% SSR rate (high base rate)
Pull 10: 100% guaranteed SSR

No soft pity needed (only 10 pulls max)
Extremely player-friendly
Encourages new player engagement
```

---

## 50/50 System Example

### Scenario: Limited Banner
```
Banner: Fire Dragon pickup
SSR rate: 2%
Pickup rateShare: 0.5 (50/50)

Pull 1-30: No SSR yet
Pull 31: SSR! → Roll 50/50
  → 50% Fire Dragon (pickup)
  → 50% Holy Knight (off-banner)
  → Result: Holy Knight (lost 50/50)

Pull 32-60: No SSR yet
Pull 61: SSR! → Guaranteed pickup (lost last 50/50)
  → 100% Fire Dragon
  → Result: Fire Dragon (won guarantee)

Pity counter resets to 0
```

---

## Multi-Pull Guarantee Example

### 10-Pull Guarantee
```
Player does 10-pull:

Scenario 1: Lucky
- 1 SSR
- 3 SR
- 6 R
Result: Guarantee already satisfied (1 SSR > 1 SR)

Scenario 2: Standard
- 0 SSR
- 2 SR
- 8 R
Result: Guarantee already satisfied (2 SR ≥ 1 SR)

Scenario 3: Unlucky (all R)
- 0 SSR
- 0 SR
- 10 R
Result: System upgrades 1 R → SR (guarantee triggers)

Final result: 1 SR, 9 R (guarantee satisfied)
```

---

## Rate Calculation Examples

### Example 1: Standard Banner
```
Banner rates:
- SSR: 2%
- SR: 18%
- R: 80%

SSR pool:
- Fire Dragon: weight 1.0
- Holy Knight: weight 1.0
Total weight: 2.0

Individual rates:
- Fire Dragon: 2% × (1.0/2.0) = 1%
- Holy Knight: 2% × (1.0/2.0) = 1%
```

### Example 2: Limited Banner (Pickup)
```
Banner rates:
- SSR: 2%

SSR pool:
- Fire Dragon (pickup): weight 0.5, isPickup true
- Holy Knight: weight 0.5, isPickup false
Total weight: 1.0

50/50 system:
- Fire Dragon: 2% × 0.5 = 1% (pickup)
- Holy Knight: 2% × 0.5 = 1% (off-banner)

After losing 50/50:
- Fire Dragon: 2% × 1.0 = 2% (guaranteed)
- Holy Knight: 0% (blocked by guarantee)
```

### Example 3: Beginner Banner
```
Banner rates:
- SSR: 5%

SSR pool (all equally likely):
- Fire Dragon: weight 0.33
- Holy Knight: weight 0.33
- Shadow Assassin: weight 0.34
Total weight: 1.0

Individual rates:
- Fire Dragon: 5% × 0.33 = 1.65%
- Holy Knight: 5% × 0.33 = 1.65%
- Shadow Assassin: 5% × 0.34 = 1.70%
```

---

## Data Validation Rules

1. **Rate Totals**:
   - SSR + SR + R = 1.0 (100%)
   - All rates must be 0.0-1.0

2. **Pity Values**:
   - soft < hard (if soft exists)
   - hard must be > 0
   - guaranteedPickupOn ≤ hard

3. **Duration Logic**:
   - start < end (if end is not null)
   - expiresAfterDays > 0 (if specified)
   - expiresAfterPulls > 0 (if specified)

4. **Pool Weights**:
   - All weights must be > 0
   - At least 1 item per rarity pool
   - isPickup can only be true for featured items

5. **Cost Consistency**:
   - multi cost ≈ 10 × single cost (standard)
   - All cost items must exist in Items.json

6. **Pickup Logic**:
   - Only 1 item should have isPickup = true per rarity
   - rateShare must be 0.0-1.0
   - guaranteedOn array values ≤ hard pity

7. **Guarantee Consistency**:
   - multiPullGuarantee.pullCount should be 10
   - firstTenPull only for beginner banners
   - pickupGuarantee only for limited banners

8. **Bonus Validation**:
   - All discount values: 0.0-1.0
   - discountedPulls ≥ 1

---

## Design Guidelines

### Rate Design
- **SSR Rate**: 1-3% industry standard
- **Soft Pity**: 70-80 pulls recommended
- **Hard Pity**: 90 pulls industry standard
- **Multi-Pull Guarantee**: At least 1 SR per 10-pull

### Banner Types
- **Permanent**: Standard pool, always available
- **Limited**: Featured characters, time-limited, better rates
- **Beginner**: New player friendly, higher rates, discounts
- **Weapon**: Equipment focus, different pity system
- **Event**: Special events, unique mechanics

### Pity Strategy
- **Standard Banner**: Higher pity (90), carries over
- **Limited Banner**: Lower pity (75-90), doesn't carry
- **Beginner Banner**: Very low pity (10), limited pulls

### Pickup System
- **50/50 First**: First SSR has 50% chance of pickup
- **Guaranteed Second**: Second SSR guaranteed pickup
- **Rate Up**: Featured character has higher weight

### Discount Strategy
- **First Pull**: 50% off to encourage trial
- **Daily Discount**: 20% off 1 pull per day
- **Beginner Discount**: 25% off all pulls

---

## Legal Compliance

### China Requirements
- Display all individual character rates
- Show cumulative probability curves
- Provide pull history
- Pity system clearly explained

### Japan Requirements
- "Complete gacha" (kompu gacha) prohibited
- Rates must be disclosed
- Pity system recommended

### Korea Requirements
- All rates publicly disclosed
- Pull history accessible
- Probability verification available

### Best Practices
- Display rates prominently in-game
- Explain pity system clearly
- Provide pull history and statistics
- Calculate and show cumulative probabilities

---

## Related Configuration Files

- **Characters.json**: Characters in banner pools
- **Items.json**: Summon scrolls and costs
- **Economy.json**: Currency balance and exchange rates
- **ShopProducts.json**: Purchase summon scrolls
- **FeatureFlags.json**: Enable/disable banners
- **KillSwitch.json**: Emergency disable problematic banners
- **SegmentConfig.json**: Segment-specific rate adjustments

---

## Version History
- **1.0.0** (2025-11-23): Initial schema documentation
