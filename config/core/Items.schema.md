# Items.json Schema Documentation

## Overview
This document provides comprehensive descriptions for all indices (fields) in the `Items.json` configuration file. This file defines all equipment, consumables, materials, currencies, and special items in the game with their stats, effects, and acquisition methods.

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

### `items` (array)
- **Type**: Array of Item Objects
- **Description**: Complete inventory of all items in the game
- **Required**: Yes

---

## Item Object Structure

### `id` (string)
- **Type**: Unique Identifier
- **Description**: Immutable item identifier used throughout the codebase
- **Format**: `ITEM_[TYPE]_[NAME]`
- **Examples**:
  - `"ITEM_SWORD_FLAME"` - Equipment
  - `"ITEM_CONSUMABLE_HP_POTION"` - Consumable
  - `"ITEM_MATERIAL_DRAGON_SCALE"` - Material
  - `"ITEM_CURRENCY_GOLD"` - Currency
- **Required**: Yes
- **Constraints**: Must be unique across all items

---

## Meta Object (Item Metadata)

### `meta` (object)
Contains display and categorization metadata for the item.

#### `meta.nameKey` (string)
- **Type**: Localization Key
- **Description**: Reference key for item's display name
- **Format**: `ITEM_NAME_[ITEM_ID]`
- **Example**: `"ITEM_NAME_SWORD_FLAME"`

#### `meta.descriptionKey` (string)
- **Type**: Localization Key
- **Description**: Reference key for item's description/lore text
- **Format**: `ITEM_DESC_[ITEM_ID]`
- **Example**: `"ITEM_DESC_SWORD_FLAME"`

#### `meta.icon` (string)
- **Type**: Asset Reference
- **Description**: Icon sprite identifier for inventory display
- **Example**: `"Icon_Weapon_Sword_Flame"`

#### `meta.model` (string | null)
- **Type**: Asset Reference
- **Description**: 3D model or sprite for item visual (on character or world)
- **Example**: `"Model_Sword_Flame"`
- **Null**: For non-visual items (currencies, materials)

#### `meta.type` (string)
- **Type**: Category Enum
- **Description**: Primary item category
- **Values**:
  - `"Weapon"` - Offensive equipment
  - `"Armor"` - Defensive equipment
  - `"Accessory"` - Jewelry and trinkets
  - `"Consumable"` - Single-use items
  - `"Material"` - Crafting/upgrade materials
  - `"Currency"` - Money and premium currency
  - `"Special"` - Event items, tickets, keys
- **Usage**: Inventory categorization, filters

#### `meta.subType` (string)
- **Type**: Subcategory Enum
- **Description**: Specific item subclass within type
- **Weapon SubTypes**: `"Sword"`, `"Axe"`, `"Bow"`, `"Staff"`, `"Dagger"`
- **Armor SubTypes**: `"HeavyArmor"`, `"LightArmor"`, `"Robe"`, `"Shield"`
- **Accessory SubTypes**: `"Ring"`, `"Amulet"`, `"Necklace"`, `"Earring"`
- **Consumable SubTypes**: `"Potion"`, `"Food"`, `"Scroll"`, `"Elixir"`
- **Material SubTypes**: `"AscensionMaterial"`, `"EnhancementMaterial"`, `"CraftingComponent"`
- **Currency SubTypes**: `"SoftCurrency"`, `"PremiumCurrency"`, `"EventCurrency"`
- **Special SubTypes**: `"GachaTicket"`, `"Key"`, `"Fragment"`

#### `meta.rarity` (string)
- **Type**: Enum
- **Description**: Item rarity tier
- **Values**: `"Common"`, `"R"`, `"SR"`, `"SSR"`, `"Premium"`
- **Usage**: Drop rates, visual styling, progression gates

#### `meta.maxLevel` (number)
- **Type**: Integer
- **Description**: Maximum enhancement level for this item
- **Range**: 1-90
- **Examples**:
  - `1` - Non-upgradable (consumables, materials)
  - `80-90` - SSR equipment
  - `70-80` - SR equipment

#### `meta.slot` (string | null)
- **Type**: Equipment Slot Enum
- **Description**: Character equipment slot this item occupies
- **Values**:
  - `"MainHand"` - Primary weapon
  - `"OffHand"` - Shield or dual-wield weapon
  - `"Helmet"` - Head armor
  - `"Chest"` - Body armor
  - `"Gloves"` - Hand armor
  - `"Boots"` - Foot armor
  - `"Ring"` - Finger slot (usually 2 slots)
  - `"Neck"` - Necklace/amulet
  - `"Earring"` - Ear slot (usually 2 slots)
- **Null**: For non-equipment items

---

## Base Stats Object (Equipment Only)

### `baseStats` (object)
Starting statistics at level 1 for equipment.

#### Common Base Stats:

##### `atk` (number)
- **Type**: Integer
- **Description**: Attack power bonus
- **Typical Range**: 70-270 depending on type and rarity

##### `def` (number)
- **Type**: Integer
- **Description**: Defense bonus
- **Typical Range**: 100-250 for armor

##### `hp` (number)
- **Type**: Integer
- **Description**: HP bonus
- **Typical Range**: 200-1000

##### `critRate` (number)
- **Type**: Float (percentage as decimal)
- **Description**: Critical hit chance bonus
- **Format**: `0.12` = 12%
- **Typical Range**: 0.05-0.20

##### `critDmg` (number)
- **Type**: Float (multiplier)
- **Description**: Critical damage bonus
- **Format**: `0.35` = +35% crit damage
- **Typical Range**: 0.25-0.50

##### `effectRes` (number)
- **Type**: Float (percentage as decimal)
- **Description**: Resistance to debuffs
- **Format**: `0.15` = 15%

---

## Stat Roll Range Object (Equipment Only)

### `statRollRange` (object)
Defines randomization range for equipment stats (loot variation).

#### Structure:
Each stat has a `min` and `max` value defining the roll range.

```json
"statRollRange": {
  "atk": {"min": 220, "max": 270},
  "critRate": {"min": 0.08, "max": 0.16}
}
```

#### `min` (number)
- **Type**: Numeric
- **Description**: Minimum possible rolled value
- **Usage**: Worst-case item roll

#### `max` (number)
- **Type**: Numeric
- **Description**: Maximum possible rolled value
- **Usage**: Best-case item roll

**Note**: Base stats should be within this range (typically at midpoint).

---

## Sub Stats Object (Equipment Only)

### `subStats` (object)
Configuration for random secondary stats on equipment.

#### `maxSubStats` (number)
- **Type**: Integer
- **Description**: Maximum number of sub-stats this item can have
- **Range**: 0-4
- **Typical Values**:
  - `4` - SSR items
  - `3` - SR items
  - `2` - R items

#### `pool` (array)
- **Type**: Array of Sub-Stat Pool Objects
- **Description**: Available sub-stats and their generation rules

##### Pool Entry Fields:

###### `stat` (string)
- **Type**: Stat Identifier
- **Description**: Sub-stat type
- **Values**: `"hp"`, `"atk%"`, `"def%"`, `"spd"`, `"critRate"`, `"critDmg"`, `"effectHit"`, `"effectRes"`, `"accuracy"`, `"evasion"`

###### `weight` (number)
- **Type**: Integer
- **Description**: Relative probability weight for random selection
- **Usage**: Higher weight = more likely to roll
- **Example**: Weight 1000 is twice as likely as weight 500

###### `valueRange` (object)
- **Type**: Min/Max Range Object
- **Description**: Value range for this sub-stat
- **Example**: `{"min": 0.05, "max": 0.12}` = 5%-12%

---

## Set Bonus Object (Equipment Only)

### `setBonus` (object | null)
Equipment set bonus information.

#### `setId` (string)
- **Type**: Set Identifier Reference
- **Description**: ID of the gear set this item belongs to
- **Format**: `SET_[NAME]`
- **Example**: `"SET_INFERNO"`
- **Links to**: GearSets.json configuration

#### `2pieces` (object)
- **Type**: Set Bonus Configuration
- **Description**: Bonus when wearing 2 pieces of this set

##### `description` (string)
- **Type**: Display Text
- **Description**: Player-facing bonus description
- **Example**: `"Fire DMG +15%"`

##### `effects` (array)
- **Type**: Array of Effect Objects
- **Description**: Mechanical effects of the bonus
- **Example**: `[{"type": "ElementalDmgBonus", "element": "Fire", "value": 0.15}]`

#### `4pieces` (object)
- **Type**: Set Bonus Configuration
- **Description**: Bonus when wearing 4 pieces of this set (stacks with 2-piece)
- **Structure**: Same as 2pieces

**Null**: For accessories and items not part of sets.

---

## Unique Effect Object (Accessory Only)

### `uniqueEffect` (object | null)
Special effects unique to this specific item (typically SSR accessories).

#### `name` (string)
- **Type**: Effect Name
- **Description**: Display name of the unique effect
- **Example**: `"Dragon's Fury"`

#### `description` (string)
- **Type**: Effect Description
- **Description**: Player-facing explanation
- **Example**: `"Increase all elemental damage by 20%. When HP is above 80%, increase ATK by 15%"`

#### `effects` (array)
- **Type**: Array of Effect Objects
- **Description**: Mechanical implementation
- **Example**:
```json
[
  {"type": "ElementalDmgBonus", "element": "All", "value": 0.20},
  {"type": "ConditionalBuff", "condition": "HP_ABOVE_80", "stat": "atk%", "value": 0.15}
]
```

---

## Enhancement Object (Equipment Only)

### `enhancement` (object)
Equipment upgrade/enhancement system configuration.

#### `maxEnhancement` (number)
- **Type**: Integer
- **Description**: Maximum enhancement level
- **Range**: 0-15
- **Typical Values**:
  - `15` - SSR equipment
  - `12` - SR equipment
  - `10` - R equipment

#### `costPerLevel` (array)
- **Type**: Array of Cost Tier Objects
- **Description**: Resource requirements at specific enhancement milestones

##### Cost Tier Fields:

###### `level` (number)
- **Type**: Integer
- **Description**: Enhancement level this cost applies to
- **Example**: `1`, `5`, `10`, `15`

###### `gold` (number)
- **Type**: Integer
- **Description**: Gold currency cost
- **Example**: `1000`

###### `materials` (array)
- **Type**: Array of Material Requirement Objects
- **Description**: Materials needed

####### Material Requirement:

- **itemId** (string): Material item ID (references Items.json)
- **count** (number): Quantity required
- **Example**: `{"itemId": "MAT_WEAPON_ORE", "count": 5}`

---

## Effect Object (Consumable Only)

### `effect` (object)
Immediate effect when consumable is used.

#### `type` (string)
- **Type**: Effect Type Enum
- **Description**: Type of effect applied
- **Values**: `"InstantHeal"`, `"InstantMana"`, `"InstantBuff"`, `"Revive"`

#### `value` (number)
- **Type**: Numeric
- **Description**: Base effect value
- **Example**: `500` for flat heal

#### `formula` (string)
- **Type**: Calculation Formula
- **Description**: Dynamic effect calculation
- **Example**: `"500 + (target.maxHp * 0.15)"` = 500 + 15% max HP

---

## Usage Rules Object

### `usageRules` (object)
Rules governing item usage and inventory behavior.

#### `usableInCombat` (boolean)
- **Type**: Boolean
- **Description**: Whether item can be used during battle
- **Example**: `true` for HP potions

#### `cooldown` (number)
- **Type**: Float (seconds)
- **Description**: Time before item can be used again
- **Example**: `5.0` = 5-second cooldown between uses

#### `maxStack` (number)
- **Type**: Integer
- **Description**: Maximum quantity in a single inventory stack
- **Range**: 1-999999999
- **Examples**:
  - `99` - Consumables
  - `9999` - Materials
  - `999999999` - Currencies

#### `consumeOnUse` (boolean)
- **Type**: Boolean
- **Description**: Whether item is destroyed when used
- **Example**: `true` for consumables

#### `tradeable` (boolean)
- **Type**: Boolean
- **Description**: Whether item can be traded between players
- **Default**: `false` for most items

#### `sellable` (boolean)
- **Type**: Boolean
- **Description**: Whether item can be sold to NPC shops
- **Example**: `true` for materials

#### `sellPrice` (number)
- **Type**: Integer
- **Description**: Gold received when selling to shop
- **Example**: `5000`

---

## Sources Array (Material Only)

### `sources` (array)
- **Type**: Array of Source Objects
- **Description**: Where and how to obtain this material

### Source Object Fields:

#### `type` (string)
- **Type**: Source Type Enum
- **Description**: Acquisition method
- **Values**: `"Drop"`, `"Shop"`, `"Craft"`, `"Event"`, `"Quest"`, `"Achievement"`

#### `sourceId` (string)
- **Type**: Source Entity ID
- **Description**: Specific source identifier
- **Examples**:
  - `"BOSS_ANCIENT_DRAGON"` for drops
  - `"SHOP_GUILD"` for shop purchases
  - `"EVENT_DRAGON_RAID"` for events

#### `dropRate` (number)
- **Type**: Float (percentage as decimal)
- **Description**: Probability of obtaining from this source
- **Example**: `0.15` = 15% drop rate
- **Usage**: Only for Drop type sources

#### `cost` (object)
- **Type**: Cost Object
- **Description**: Purchase price in shop
- **Fields**:
  - **itemId** (string): Currency item ID
  - **amount** (number): Cost quantity
- **Example**: `{"itemId": "CURRENCY_GUILD_COIN", "amount": 500}`
- **Usage**: Only for Shop type sources

---

## IAP Mapping Object (Premium Currency Only)

### `iapMapping` (object)
In-app purchase configuration for premium currency.

#### `canPurchase` (boolean)
- **Type**: Boolean
- **Description**: Whether this currency can be bought with real money
- **Example**: `true`

#### `exchangeRate` (number)
- **Type**: Float
- **Description**: Conversion rate from IAP to in-game currency
- **Example**: `1.0` = $1 USD = 1 gem (adjusted by IAP packages)

---

## Gacha Mapping Object (Special Items Only)

### `gachaMapping` (object)
Configuration for gacha/summon ticket items.

#### `validBanners` (array)
- **Type**: Array of Banner IDs
- **Description**: Which gacha banners this ticket can be used on
- **Example**: `["BANNER_STANDARD", "BANNER_FIRE_DRAGON_V1"]`

#### `pullCount` (number)
- **Type**: Integer
- **Description**: Number of pulls this ticket grants
- **Example**: `1` for single pull, `10` for multi-pull ticket

---

## Assets Object

### `assets` (object)
References to item's visual and audio assets.

#### `assets.prefab` (string)
- **Type**: Addressable Asset Path
- **Description**: 3D model prefab for equipment on character
- **Format**: `"Addressables/Items/[Category]/[Name]/Prefab"`
- **Usage**: Character equipment visualization

#### `assets.icon` (string)
- **Type**: Addressable Asset Path
- **Description**: Icon sprite for inventory
- **Format**: `"Addressables/UI/Icons/Items/[Name]"`
- **Usage**: Inventory UI, tooltips, shop displays

#### `assets.vfx` (string)
- **Type**: Addressable Asset Path
- **Description**: Visual effect when item is used
- **Format**: `"Addressables/VFX/Items/[Name]"`
- **Usage**: Consumable use effects
- **Optional**: Only for consumables

---

## Item Type Field Summary

### Equipment (Weapon, Armor, Accessory)
**Has**: meta, baseStats, statRollRange, subStats, setBonus/uniqueEffect, enhancement, assets

**Does NOT have**: effect, usageRules (except maxStack), sources, iapMapping, gachaMapping

### Consumable
**Has**: meta, effect, usageRules, assets

**Does NOT have**: baseStats, statRollRange, subStats, setBonus, enhancement, sources

### Material
**Has**: meta, usageRules (stack/trade/sell), sources, assets

**Does NOT have**: baseStats, subStats, setBonus, enhancement, effect

### Currency
**Has**: meta, usageRules (maxStack), iapMapping (if premium), assets

**Does NOT have**: Most equipment/consumable fields

### Special (Gacha Tickets, etc.)
**Has**: meta, usageRules, gachaMapping, assets

**Does NOT have**: Equipment stats, consumable effects

---

## Item Naming Conventions

### Item IDs
- **Format**: `ITEM_[TYPE]_[NAME]`
- **Examples**:
  - `ITEM_SWORD_FLAME`
  - `ITEM_ARMOR_ICE_PLATE`
  - `ITEM_CONSUMABLE_HP_POTION`
  - `ITEM_MATERIAL_DRAGON_SCALE`

### Material IDs
- **Format**: `MAT_[CATEGORY]_[NAME]`
- **Examples**:
  - `MAT_WEAPON_ORE`
  - `MAT_DRAGON_SCALE`
  - `MAT_LEGENDARY_GEM`

### Currency IDs
- **Format**: `CURRENCY_[TYPE]`
- **Examples**:
  - `CURRENCY_GOLD`
  - `CURRENCY_GEM`
  - `CURRENCY_GUILD_COIN`

---

## Data Validation Rules

1. **Unique IDs**: All item IDs must be unique across entire items array
2. **Stat Consistency**: statRollRange.min ≤ baseStats ≤ statRollRange.max
3. **Type-Field Matching**: Equipment must have baseStats, consumables must have effect
4. **Slot Validation**: Only equipment can have non-null slot values
5. **Material References**: All itemIds in materials arrays must exist in Items.json
6. **Set References**: All setIds must exist in GearSets.json
7. **Enhancement Progression**: costPerLevel array should have increasing level and cost values
8. **Sub-stat Pool Weights**: All weights should be positive integers
9. **Rarity Constraints**: Higher rarity items should have higher stat totals

---

## Equipment Stat Calculation Example

### Level 1 Sword
```
Base ATK: 245 (from baseStats)
Rolled ATK: 255 (random between 220-270 from statRollRange)
Sub-stats:
  - atk%: 0.12 (12%)
  - spd: 10
  - effectHit: 0.08 (8%)
```

### At Max Level (90)
```
ATK: 255 * (level scaling multiplier from GrowthCurves.json)
All sub-stats and bonuses: Applied additively/multiplicatively per game rules
Set Bonus: Applied if wearing 2/4 pieces
```

---

## Common Use Cases

### Finding Best Weapon for Fire DPS
1. Filter `type: "Weapon"`, `rarity: "SSR"`
2. Check `setBonus.setId` for `"SET_INFERNO"`
3. Compare `baseStats.atk` and `critDmg` values
4. Review `statRollRange` for potential max rolls

### Checking Material Requirements
1. Look up character's ascension materials in Characters.json
2. Find material items by matching `id` values
3. Check `sources` array for acquisition methods
4. Determine if purchasable via shop or must farm

### Consumable Planning
1. Check `usageRules.usableInCombat` for battle items
2. Review `effect.formula` for healing amount
3. Verify `usageRules.cooldown` for usage frequency
4. Check `usageRules.maxStack` for inventory capacity

---

## Related Configuration Files

- **Characters.json**: Character ascension requires materials from Items.json
- **GearSets.json**: Equipment references set bonuses
- **GrowthCurves.json**: Equipment stat scaling with level
- **Skills.json**: Some effects reference item buffs
- **Economy.json**: Item prices, drop rates, shop inventory
- **ShopProducts.json**: Purchasable items and bundles
- **Localization/**: Item names and descriptions

---

## Version History

- **1.0.0** (2025-11-23): Initial schema documentation
