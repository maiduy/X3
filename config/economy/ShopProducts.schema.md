# ShopProducts.json Schema Documentation

## Overview
Defines in-game shops, IAP products, currency exchange stores, and daily deals. Controls the monetization system, product catalog, pricing, limits, and refresh schedules.

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

### `shops` (array)
- **Type**: Array of Shop Objects
- **Description**: All shop configurations
- **Required**: Yes

---

## Shop Object

### `id` (string)
- **Format**: `SHOP_[NAME]`
- **Example**: `"SHOP_GEM_STORE"`, `"SHOP_ARENA"`, `"SHOP_GUILD"`
- **Description**: Unique shop identifier
- **Required**: Yes

### `meta` (object)
Shop metadata and display information.

#### `nameKey` (string)
- **Type**: Localization Key
- **Description**: Shop name localization key
- **Format**: `SHOP_NAME_[SHOP_ID]`
- **Example**: `"SHOP_NAME_GEM_STORE"`

#### `descriptionKey` (string)
- **Type**: Localization Key
- **Description**: Shop description localization key
- **Format**: `SHOP_DESC_[SHOP_ID]`
- **Example**: `"SHOP_DESC_GEM_STORE"`

#### `icon` (string)
- **Type**: Asset Reference
- **Description**: Shop icon asset path
- **Example**: `"Icon_Shop_GemStore"`

#### `type` (string)
- **Values**: `"IAP"`, `"Currency"`, `"Special"`, `"Event"`
- **Description**: Shop category type
- **Required**: Yes

**Shop Types**:
- **IAP**: In-app purchases with real money
- **Currency**: Buy with in-game currency (Arena coins, Guild coins, etc.)
- **Special**: Daily deals, limited offers
- **Event**: Event-specific shops

#### `unlockLevel` (number)
- **Type**: Integer
- **Description**: Player level required to unlock shop
- **Example**: `1`, `15`, `20`
- **Default**: `1`
- **Usage**: Gate shops behind progression

### `currency` (string)
- **Type**: Currency ID
- **Description**: Primary currency for this shop
- **Example**: `"CURRENCY_ARENA_COIN"`, `"CURRENCY_GUILD_COIN"`
- **Optional**: Only for Currency type shops
- **Usage**: Currency exchange shops

### `products` (array)
- **Type**: Array of Product Objects
- **Description**: Products available in this shop
- **Required**: Yes

### `refreshSchedule` (object)
Shop refresh/reset schedule.

#### `type` (string)
- **Values**: `"Daily"`, `"Weekly"`, `"Monthly"`, `"Never"`
- **Description**: Refresh frequency
- **Default**: `"Never"` (IAP shops)

#### `resetTime` (string)
- **Type**: Time String (HH:MM:SS)
- **Description**: Daily reset time (UTC)
- **Example**: `"00:00:00"` = midnight UTC
- **Optional**: Only for Daily refresh

#### `resetDay` (number)
- **Type**: Integer (1-7)
- **Description**: Weekly reset day (1 = Monday)
- **Example**: `1` = Monday reset
- **Optional**: Only for Weekly refresh

---

## Product Object

### `id` (string)
- **Format**: `IAP_[NAME]` or `[SHOP]_[NAME]`
- **Example**: `"IAP_GEM_PACK_SMALL"`, `"ARENA_SUMMON_SCROLL"`
- **Description**: Unique product identifier
- **Required**: Yes

### `meta` (object)
Product metadata.

#### `nameKey` (string)
- **Type**: Localization Key
- **Description**: Product name localization key
- **Format**: `IAP_NAME_[PRODUCT]` or `PRODUCT_NAME_[ITEM]`
- **Example**: `"IAP_NAME_GEM_SMALL"`

#### `descriptionKey` (string)
- **Type**: Localization Key
- **Description**: Product description localization key
- **Format**: `IAP_DESC_[PRODUCT]` or `PRODUCT_DESC_[ITEM]`
- **Example**: `"IAP_DESC_GEM_SMALL"`
- **Optional**: Some products don't need descriptions

#### `icon` (string)
- **Type**: Asset Reference
- **Description**: Product icon asset path
- **Example**: `"Icon_IAP_GemSmall"`

#### `displayOrder` (number)
- **Type**: Integer
- **Description**: Display order in shop (lower = earlier)
- **Example**: `1`, `2`, `3`
- **Usage**: Control product positioning

#### `badge` (string)
- **Values**: `"POPULAR"`, `"BEST_VALUE"`, `"LIMITED"`, `"NEW"`, `"DAILY"`
- **Description**: Special badge displayed on product
- **Example**: `"BEST_VALUE"`
- **Optional**: Only for featured products

---

## Price Object

### IAP Price (Real Money)
```json
{
  "price": {
    "usd": 0.99,
    "sku": "com.game.gem.pack.small"
  }
}
```

#### `usd` (number)
- **Type**: Float (USD)
- **Description**: Price in US dollars
- **Example**: `0.99`, `4.99`, `9.99`
- **Required**: For IAP products

#### `sku` (string)
- **Type**: Product SKU
- **Description**: Store product identifier
- **Format**: `com.game.[category].[product]`
- **Example**: `"com.game.gem.pack.small"`
- **Required**: For IAP products
- **Usage**: App Store / Google Play product ID

### Currency Price (In-Game)
```json
{
  "price": [
    {"itemId": "CURRENCY_ARENA_COIN", "amount": 200}
  ]
}
```

#### Structure
- **Type**: Array of Cost Items
- **Description**: In-game currency cost
- **Example**: `[{"itemId": "CURRENCY_GEM", "amount": 50}]`
- **Usage**: Currency exchange shops

---

## Contents Array

### Structure
```json
{
  "contents": [
    {"itemId": "CURRENCY_GEM", "amount": 60}
  ]
}
```

- **Type**: Array of Reward Objects
- **Description**: Items player receives
- **Structure**: `[{"itemId": "...", "amount": number}]`
- **Required**: Yes

---

## Bonus Contents Array

### Structure
```json
{
  "bonusContents": [
    {"itemId": "CURRENCY_GEM", "amount": 10, "condition": "FirstPurchase"}
  ]
}
```

#### Bonus Item Object

##### `itemId` (string)
- **Type**: Item ID
- **Description**: Bonus item identifier
- **Example**: `"CURRENCY_GEM"`

##### `amount` (number)
- **Type**: Integer
- **Description**: Bonus item quantity
- **Example**: `10`

##### `condition` (string)
- **Values**: `"FirstPurchase"`, `"DailyBonus"`, `"EventBonus"`
- **Description**: Condition for bonus
- **Example**: `"FirstPurchase"` = one-time first purchase bonus

---

## Daily Rewards Object

### Structure
```json
{
  "dailyRewards": {
    "duration": 30,
    "rewards": [
      {"itemId": "CURRENCY_GEM", "amount": 60},
      {"itemId": "CURRENCY_GOLD", "amount": 10000}
    ]
  }
}
```

### `duration` (number)
- **Type**: Integer (days)
- **Description**: Number of days rewards are granted
- **Example**: `30` = 30 days of daily rewards
- **Usage**: Monthly passes, battle passes

### `rewards` (array)
- **Type**: Array of Reward Objects
- **Description**: Daily reward items
- **Example**: `[{"itemId": "CURRENCY_GEM", "amount": 60}]`
- **Usage**: Player receives these every day for X days

---

## Limits Object

### `perAccount` (number | null)
- **Type**: Integer or null
- **Description**: Maximum purchases per account (lifetime)
- **Example**: `1` = can only buy once ever
- **Null**: No account limit
- **Usage**: One-time packs, monthly passes

### `perDay` (number | null)
- **Type**: Integer or null
- **Description**: Maximum purchases per day
- **Example**: `3` = 3 per day
- **Null**: No daily limit
- **Usage**: Daily deals, energy refills

### `perWeek` (number | null)
- **Type**: Integer or null
- **Description**: Maximum purchases per week
- **Example**: `5` = 5 per week
- **Null**: No weekly limit
- **Usage**: Weekly shop items

### `perMonth` (number | null)
- **Type**: Integer or null
- **Description**: Maximum purchases per month
- **Example**: `30` = 30 per month
- **Null**: No monthly limit
- **Usage**: Monthly shop items

### `renewalAllowed` (boolean)
- **Description**: Allow renewal after expiration
- **Default**: `false`
- **Usage**: Monthly passes can be renewed

---

## Availability Object

### `startDate` (string)
- **Type**: ISO 8601 DateTime
- **Description**: When product becomes available
- **Example**: `"2025-01-01T00:00:00Z"`
- **Required**: Yes

### `endDate` (string | null)
- **Type**: ISO 8601 DateTime or null
- **Description**: When product expires
- **Example**: `"2025-12-31T23:59:59Z"`
- **Null**: Permanent availability
- **Usage**: Limited-time offers

---

## Discount Field

### `discount` (number)
- **Type**: Float (0.0-1.0)
- **Description**: Discount percentage off regular price
- **Example**: `0.5` = 50% off
- **Optional**: Only for discounted products
- **Usage**: Daily deals, special offers

**Price Calculation**:
```
Regular price: 100 gems
Discount: 0.5 (50%)
Final price: 100 × (1 - 0.5) = 50 gems
```

---

## Shop Type Examples

### IAP Shop (Gem Store)
```json
{
  "id": "SHOP_GEM_STORE",
  "meta": {
    "type": "IAP",
    "unlockLevel": 1
  },
  "products": [
    {
      "id": "IAP_GEM_PACK_SMALL",
      "price": {
        "usd": 0.99,
        "sku": "com.game.gem.pack.small"
      },
      "contents": [
        {"itemId": "CURRENCY_GEM", "amount": 60}
      ],
      "bonusContents": [
        {"itemId": "CURRENCY_GEM", "amount": 10, "condition": "FirstPurchase"}
      ]
    }
  ]
}
```

**Characteristics**:
- Real money purchases
- First purchase bonus (+10 gems)
- No purchase limits
- Always available

### Currency Shop (Arena)
```json
{
  "id": "SHOP_ARENA",
  "meta": {
    "type": "Currency",
    "unlockLevel": 15
  },
  "currency": "CURRENCY_ARENA_COIN",
  "products": [
    {
      "id": "ARENA_SUMMON_SCROLL",
      "price": [
        {"itemId": "CURRENCY_ARENA_COIN", "amount": 200}
      ],
      "contents": [
        {"itemId": "ITEM_SUMMON_SCROLL", "amount": 1}
      ],
      "limits": {
        "perWeek": 5
      }
    }
  ],
  "refreshSchedule": {
    "type": "Daily",
    "resetTime": "00:00:00"
  }
}
```

**Characteristics**:
- Unlocks at level 15
- Uses Arena Coins currency
- Weekly purchase limits
- Daily refresh

### Special Shop (Daily Deals)
```json
{
  "id": "SHOP_DAILY_DEALS",
  "meta": {
    "type": "Special",
    "unlockLevel": 5
  },
  "products": [
    {
      "id": "DAILY_GOLD_DEAL",
      "price": [
        {"itemId": "CURRENCY_GEM", "amount": 50}
      ],
      "contents": [
        {"itemId": "CURRENCY_GOLD", "amount": 50000}
      ],
      "limits": {
        "perDay": 1
      },
      "discount": 0.5
    }
  ],
  "refreshSchedule": {
    "type": "Daily",
    "resetTime": "00:00:00"
  }
}
```

**Characteristics**:
- Unlocks at level 5
- 50% discount
- 1 purchase per day
- Daily refresh

---

## Product Type Examples

### Small Gem Pack
```json
{
  "id": "IAP_GEM_PACK_SMALL",
  "price": {"usd": 0.99},
  "contents": [
    {"itemId": "CURRENCY_GEM", "amount": 60}
  ],
  "bonusContents": [
    {"itemId": "CURRENCY_GEM", "amount": 10, "condition": "FirstPurchase"}
  ]
}
```

**Value**: $0.99 = 60 gems (+10 first time)
**Rate**: ~60 gems per dollar (base)

### Monthly Pass
```json
{
  "id": "IAP_MONTHLY_PASS",
  "price": {"usd": 4.99},
  "contents": [
    {"itemId": "CURRENCY_GEM", "amount": 300}
  ],
  "dailyRewards": {
    "duration": 30,
    "rewards": [
      {"itemId": "CURRENCY_GEM", "amount": 60},
      {"itemId": "CURRENCY_GOLD", "amount": 10000}
    ]
  },
  "limits": {
    "perAccount": 1,
    "renewalAllowed": true
  }
}
```

**Value**:
- Instant: 300 gems
- Daily: 60 gems × 30 days = 1,800 gems
- Daily: 10,000 gold × 30 days = 300,000 gold
- **Total**: 2,100 gems + 300,000 gold for $4.99

**Rate**: ~420 gems per dollar (excellent value)

### Arena Shop Item
```json
{
  "id": "ARENA_CHAR_FRAGMENT",
  "price": [
    {"itemId": "CURRENCY_ARENA_COIN", "amount": 50}
  ],
  "contents": [
    {"itemId": "CHAR_FIRE_DRAGON_FRAGMENT", "amount": 1}
  ],
  "limits": {
    "perMonth": 30
  }
}
```

**Value**: 50 Arena Coins = 1 Fragment
**Monthly Cap**: 30 fragments (1,500 coins)
**Usage**: F2P character acquisition path

### Daily Deal
```json
{
  "id": "DAILY_GOLD_DEAL",
  "price": [
    {"itemId": "CURRENCY_GEM", "amount": 50}
  ],
  "contents": [
    {"itemId": "CURRENCY_GOLD", "amount": 50000}
  ],
  "limits": {
    "perDay": 1
  },
  "discount": 0.5
}
```

**Value**: 50 gems = 50,000 gold (50% off)
**Regular Price**: 100 gems = 50,000 gold
**Exchange Rate**: 1 gem = 1,000 gold (discounted)

---

## Shop Refresh Examples

### Daily Refresh
```json
{
  "refreshSchedule": {
    "type": "Daily",
    "resetTime": "00:00:00"
  }
}
```

**Behavior**:
- Resets at midnight UTC every day
- Purchase limits reset
- Product availability refreshes
- Daily deals rotate

### Weekly Refresh
```json
{
  "refreshSchedule": {
    "type": "Weekly",
    "resetDay": 1
  }
}
```

**Behavior**:
- Resets every Monday (day 1)
- Weekly limits reset
- Products may rotate
- Guild shop typical pattern

### Never Refresh
```
No refreshSchedule object
```

**Behavior**:
- IAP shops never need refresh
- Products always available
- Limits are lifetime/per-account only

---

## Pricing Strategy Examples

### Entry-Level ($0.99-$4.99)
```
$0.99 = 60 gems (starter pack)
$1.99 = 130 gems (good value for low spenders)
$4.99 = 330 gems OR monthly pass (best value tier)
```

**Target**: New players, casual spenders, first purchase

### Mid-Tier ($9.99-$19.99)
```
$9.99 = 680 gems (most popular)
$19.99 = 1,400 gems (better rate)
```

**Target**: Regular players, dolphins

### High-Tier ($49.99-$99.99)
```
$49.99 = 3,500 gems
$99.99 = 7,200 gems (best rate)
```

**Target**: Whales, high spenders

### Bonus Scaling
```
$0.99: +10 gems (16% bonus)
$4.99: +50 gems (15% bonus)
$9.99: +100 gems (15% bonus)
$99.99: +1,500 gems (21% bonus)
```

**Pattern**: Higher tiers get slightly better bonuses

---

## Value Comparison

### Gem Packs
```
Small ($0.99): 60 gems = 60.6 gems/$
Medium ($4.99): 330 gems = 66.1 gems/$
Large ($9.99): 680 gems = 68.1 gems/$
Mega ($99.99): 7,200 gems = 72.0 gems/$

Bonus (first time):
Small: 70 gems = 70.7 gems/$
Medium: 380 gems = 76.2 gems/$
Large: 780 gems = 78.1 gems/$
```

**Insight**: Higher packs = better value

### Monthly Pass
```
Cost: $4.99
Instant: 300 gems
Daily: 60 gems × 30 = 1,800 gems
Total: 2,100 gems = 420.8 gems/$

Comparison:
Best gem pack: 72 gems/$
Monthly pass: 420 gems/$ (5.8× better!)
```

**Insight**: Monthly pass is best value by far

### Currency Exchange
```
Gem to Gold (shop): 50 gems = 50,000 gold (1,000 gold/gem)
Gem to Energy: 30 gems = 100 energy (3.3 energy/gem)

Gold per $:
$4.99 → 330 gems → 330,000 gold = 66,132 gold/$
```

---

## Data Validation Rules

1. **Price Consistency**:
   - IAP products must have usd and sku
   - Currency products must have price array
   - All prices > 0

2. **Contents Required**:
   - All products must have contents array
   - Contents must have at least 1 item
   - All item amounts > 0

3. **Limit Logic**:
   - All limit values ≥ 1 (if not null)
   - At least one limit should be set for limited products
   - perAccount + renewalAllowed: compatible for passes

4. **Availability**:
   - startDate < endDate (if endDate not null)
   - Products past endDate should be removed/disabled

5. **Bonus Conditions**:
   - condition must be valid condition type
   - FirstPurchase bonus: perAccount should be null or renewalAllowed

6. **Daily Rewards**:
   - duration must be ≥ 1
   - rewards array must have at least 1 item
   - Only for subscription-type products

7. **Discount Range**:
   - discount must be 0.0-1.0
   - Typically 0.1-0.75 (10-75% off)

8. **Shop Type Consistency**:
   - IAP shops: no currency field
   - Currency shops: must have currency field
   - Special shops: should have refreshSchedule

9. **Display Order**:
   - Unique display orders recommended
   - Lower numbers appear first

---

## Design Guidelines

### Pricing Tiers
- **$0.99-$1.99**: Impulse purchases, first-time buyers
- **$4.99**: Sweet spot, best value tier (monthly pass)
- **$9.99**: Most popular single purchase
- **$19.99-$49.99**: Mid-tier whales
- **$99.99**: Whale bait, highest single purchase

### Value Scaling
- Higher price = better value per dollar
- 10-20% better value per tier jump
- Monthly pass = 5-10× better than direct purchase
- First purchase bonus = 15-20% extra

### Purchase Limits
- **No Limits**: Basic gem packs
- **Per Account**: One-time packs, monthly passes
- **Per Day**: Daily deals, energy refills (3-5 max)
- **Per Week**: Arena shop items (5-10 max)
- **Per Month**: Guild shop items, character fragments (30-50 max)

### Shop Unlock Levels
- **Level 1**: Gem store (always available)
- **Level 5**: Daily deals (early engagement)
- **Level 15**: Arena shop (PvP unlocked)
- **Level 20**: Guild shop (guild unlocked)

### Refresh Timing
- **IAP Shops**: Never refresh (always available)
- **Daily Shops**: Midnight UTC (daily)
- **Weekly Shops**: Monday (weekly)
- **Event Shops**: Event duration only

---

## Monetization Strategy

### F2P Path
```
Arena Shop: Fragments, scrolls (weekly limits)
Guild Shop: Materials, resources (weekly limits)
Daily Deals: Gem → Gold conversion (daily)
```

**Goal**: Provide viable F2P progression

### Conversion Strategy
```
Step 1: $0.99 starter pack (low barrier to entry)
Step 2: $4.99 monthly pass (best value, recurring)
Step 3: $9.99 gem packs (regular purchases)
Step 4: $19.99+ for whales
```

**Goal**: Convert F2P → small spenders → regular spenders

### Retention
```
Monthly Pass: Daily login incentive
Daily Deals: Daily check-in habit
Limited Offers: FOMO (fear of missing out)
```

**Goal**: Increase DAU and retention

---

## Related Configuration Files

- **Economy.json**: Currency definitions, exchange rates, inflation control
- **Banners.json**: Gacha banners that consume gems/scrolls
- **Items.json**: Items sold in shops
- **Characters.json**: Character fragments in shops
- **SegmentConfig.json**: Segment-specific shop offers
- **FeatureFlags.json**: Enable/disable shops
- **KillSwitch.json**: Emergency disable IAP

---

## Version History
- **1.0.0** (2025-11-23): Initial schema documentation
