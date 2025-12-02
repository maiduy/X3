# LocalizationKeys.json Schema Documentation

## Overview
This document provides comprehensive descriptions for all indices (fields) in the `LocalizationKeys.json` configuration file. This file defines the complete key mapping structure for the game's multi-language localization system, organizing all translatable text into logical categories.

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

### `keyMapping` (object)
- **Type**: Object containing category definitions
- **Description**: Complete mapping of all localization keys organized by functional categories
- **Required**: Yes
- **Usage**: Defines the structure and organization of all translatable content

---

## Key Mapping Object Structure

Each category within `keyMapping` follows this structure:

### Category Object Fields

#### `category` (string)
- **Type**: Display Name
- **Description**: Human-readable name for this category
- **Usage**: Documentation, admin tools, and developer reference
- **Example**: `"Character Information"`

#### `description` (string)
- **Type**: Descriptive Text
- **Description**: Explanation of what this category contains and when to use it
- **Usage**: Helps developers and localizers understand the context
- **Example**: `"Character names and descriptions"`

#### `keys` (array of strings)
- **Type**: Array of Localization Key Identifiers
- **Description**: Complete list of all keys in this category
- **Required**: Yes
- **Usage**: Reference for what needs translation, validation checks
- **Example**: `["CHAR_NAME_FIRE_DRAGON", "CHAR_DESC_FIRE_DRAGON"]`

#### `totalKeys` (number)
- **Type**: Integer Count
- **Description**: Total number of keys in this category
- **Usage**: Quick reference, validation, progress tracking for translation
- **Example**: `10`

#### `pattern` (string)
- **Type**: Naming Pattern Description (optional)
- **Description**: Describes the naming convention for keys in this category
- **Usage**: Helps developers create new keys following established patterns
- **Example**: `"CHAR_NAME_* and CHAR_DESC_*"`

#### `dynamicParameters` (array of strings)
- **Type**: Array of Parameter Placeholders (optional)
- **Description**: List of dynamic parameters that can be injected into translations
- **Format**: `{parameterName}` in camelCase within curly braces
- **Usage**: Runtime text substitution for dynamic content
- **Example**: `["{time}", "{eventName}", "{count}"]`

---

## Key Mapping Categories

### 1. Common UI Elements
**Category Key**: `common`

**Purpose**: Basic UI buttons, labels, and common actions used throughout the game interface.

**Keys Included**:
- Basic Actions: `ok`, `cancel`, `confirm`, `close`, `back`, `next`
- CRUD Operations: `save`, `delete`
- Status Messages: `loading`, `error`, `success`, `warning`
- Binary Choices: `yes`, `no`
- Game Terms: `level`, `max`, `new`, `unlock`

**Total Keys**: 18

**Usage Context**:
- Dialog boxes and confirmation prompts
- Navigation buttons
- System messages
- Generic UI elements that appear across multiple screens

**Example Implementation**:
```csharp
// Unity C# usage
string confirmText = LocalizationManager.GetText("common", "confirm");
string errorText = LocalizationManager.GetText("common", "error");
```

---

### 2. Character Information
**Category Key**: `characters`

**Purpose**: Character names and lore descriptions for all playable characters in the roster.

**Naming Pattern**:
- **Names**: `CHAR_NAME_{CHARACTER_ID}`
- **Descriptions**: `CHAR_DESC_{CHARACTER_ID}`

**Keys Included**:
- `CHAR_NAME_FIRE_DRAGON` / `CHAR_DESC_FIRE_DRAGON`
- `CHAR_NAME_FROST_MAGE` / `CHAR_DESC_FROST_MAGE`
- `CHAR_NAME_HOLY_KNIGHT` / `CHAR_DESC_HOLY_KNIGHT`
- `CHAR_NAME_SHADOW_ASSASSIN` / `CHAR_DESC_SHADOW_ASSASSIN`
- `CHAR_NAME_NATURE_HEALER` / `CHAR_DESC_NATURE_HEALER`

**Total Keys**: 10 (5 characters × 2 keys each)

**Configuration Integration**:
- Referenced in `Characters.json` via `meta.nameKey` and `meta.descriptionKey`
- Must maintain 1:1 mapping with character definitions

**Usage Context**:
- Character selection screens
- Character detail panels
- Gacha/summon results
- Team composition UI
- Collection/roster display

**Translation Guidelines**:
- **Names**: Keep concise (max 30 characters), maintain tone/style consistency
- **Descriptions**: Character backstory and role description (max 500 characters)

**Example Implementation**:
```csharp
// Retrieve character name from Characters.json config
Character firedragon = CharacterConfig.GetCharacter("CHAR_FIRE_DRAGON");
string name = LocalizationManager.GetText("characters", firedragon.meta.nameKey);
string desc = LocalizationManager.GetText("characters", firedragon.meta.descriptionKey);
```

---

### 3. Skill Information
**Category Key**: `skills`

**Purpose**: Skill names and mechanical descriptions for all character abilities.

**Naming Pattern**:
- **Names**: `SKILL_NAME_{SKILL_ID}`
- **Descriptions**: `SKILL_DESC_{SKILL_ID}`

**Keys Included**:
- `SKILL_NAME_PHOENIX_STRIKE` / `SKILL_DESC_PHOENIX_STRIKE`
- `SKILL_NAME_BLIZZARD` / `SKILL_DESC_BLIZZARD`
- `SKILL_NAME_DIVINE_HEALING` / `SKILL_DESC_DIVINE_HEALING`
- `SKILL_NAME_DEATH_MARK` / `SKILL_DESC_DEATH_MARK`
- `SKILL_NAME_FIRE_MASTERY` / `SKILL_DESC_FIRE_MASTERY`
- `SKILL_NAME_BACKSTAB` / `SKILL_DESC_BACKSTAB`

**Total Keys**: 12 (6 skills × 2 keys each)

**Configuration Integration**:
- Referenced in `Skills.json` via `nameKey` and `descriptionKey`
- Must include damage values, cooldowns, and effect descriptions

**Usage Context**:
- Skill tooltips in battle
- Character skill panel
- Skill upgrade screens
- Skill unlock notifications

**Translation Guidelines**:
- **Names**: Evocative, thematic (max 40 characters)
- **Descriptions**: Clear mechanics explanation, include:
  - Damage multipliers (e.g., "Deals 250% ATK as damage")
  - Cooldown/cost information
  - Special effects (stun, burn, heal)
  - Target type (single, AoE)

**Example Implementation**:
```csharp
Skill phoenixStrike = SkillConfig.GetSkill("SKILL_PHOENIX_STRIKE");
string skillName = LocalizationManager.GetText("skills", phoenixStrike.nameKey);
string skillDesc = LocalizationManager.GetText("skills", phoenixStrike.descriptionKey);
```

---

### 4. Item Information
**Category Key**: `items`

**Purpose**: Item names and descriptions for all collectible items, equipment, consumables, and materials.

**Naming Pattern**:
- **Names**: `ITEM_NAME_{ITEM_ID}`
- **Descriptions**: `ITEM_DESC_{ITEM_ID}`

**Keys Included**:
- **Equipment**: `ITEM_NAME_SWORD_FLAME`, `ITEM_NAME_ARMOR_ICE_PLATE`, `ITEM_NAME_DRAGON_AMULET`
- **Consumables**: `ITEM_NAME_HP_POTION`
- **Materials**: `ITEM_NAME_DRAGON_SCALE`
- **Currency**: `ITEM_NAME_GOLD`, `ITEM_NAME_GEM`
- **Special**: `ITEM_NAME_SUMMON_SCROLL`

**Total Keys**: 16 (8 items × 2 keys each)

**Configuration Integration**:
- Referenced in `Items.json` via `nameKey` and `descriptionKey`
- Must match item types and functionality

**Usage Context**:
- Inventory screens
- Item tooltips
- Shop displays
- Reward notifications
- Crafting/enhancement UI

**Translation Guidelines**:
- **Names**: Item type + modifier (max 50 characters)
- **Descriptions**: Include:
  - Item category (equipment slot, consumable type, material tier)
  - Primary stats or effects
  - Usage instructions if applicable
  - Rarity indicators

**Example Implementation**:
```csharp
Item flameSword = ItemConfig.GetItem("ITEM_NAME_SWORD_FLAME");
string itemName = LocalizationManager.GetText("items", flameSword.nameKey);
string itemDesc = LocalizationManager.GetText("items", flameSword.descriptionKey);
```

---

### 5. Gear Set Information
**Category Key**: `gearSets`

**Purpose**: Gear set names and set bonus descriptions for equipment synergies.

**Naming Pattern**:
- **Names**: `SET_NAME_{SET_ID}`
- **Descriptions**: `SET_DESC_{SET_ID}`

**Keys Included**:
- `SET_NAME_INFERNO` / `SET_DESC_INFERNO`
- `SET_NAME_GLACIER` / `SET_DESC_GLACIER`
- `SET_NAME_LIFESTEAL` / `SET_DESC_LIFESTEAL`
- `SET_NAME_SPEED` / `SET_DESC_SPEED`
- `SET_NAME_COUNTER` / `SET_DESC_COUNTER`
- `SET_NAME_IMMUNITY` / `SET_DESC_IMMUNITY`

**Total Keys**: 12 (6 sets × 2 keys each)

**Configuration Integration**:
- Referenced in `GearSets.json` via `nameKey` and `descriptionKey`
- Must describe set bonuses at different piece counts (2-piece, 4-piece, 6-piece)

**Usage Context**:
- Equipment detail screens
- Set bonus tooltips
- Character stats panel (active sets)
- Equipment recommendation systems

**Translation Guidelines**:
- **Names**: Thematic, reflects set bonus theme
- **Descriptions**:
  - List bonuses at each tier (2-piece: +X, 4-piece: +Y)
  - Clearly state stat increases or special effects
  - Use consistent formatting across all sets

**Example Implementation**:
```csharp
GearSet infernoSet = GearSetConfig.GetSet("SET_INFERNO");
string setName = LocalizationManager.GetText("gearSets", infernoSet.nameKey);
string setDesc = LocalizationManager.GetText("gearSets", infernoSet.descriptionKey);
```

---

### 6. UI Navigation
**Category Key**: `ui`

**Purpose**: Menu items, screen titles, and navigation labels for the game interface.

**Keys Included**:
- Main Screens: `main_menu`, `battle`, `heroes`, `inventory`, `shop`, `arena`
- Features: `quests`, `events`, `settings`, `profile`, `summon`, `campaign`
- Sub-categories: `daily_quests`, `weekly_quests`, `achievements`, `leaderboard`
- Social: `friends`, `guild`, `mail`

**Total Keys**: 19

**Usage Context**:
- Main navigation menu
- Screen headers
- Tab labels
- Breadcrumb navigation

**Translation Guidelines**:
- Keep concise (max 20 characters for menu items)
- Use title case in English, follow language conventions
- Maintain parallel structure (all nouns or all verbs)

**Example Implementation**:
```csharp
string heroesLabel = LocalizationManager.GetText("ui", "heroes");
string settingsLabel = LocalizationManager.GetText("ui", "settings");
```

---

### 7. Character Stats
**Category Key**: `stats`

**Purpose**: Stat labels and abbreviations for character attributes.

**Keys Included**:
- Core Stats: `hp`, `atk`, `def`, `spd`
- Advanced Stats: `crit`, `critDmg`, `accuracy`, `evasion`, `effectRes`

**Total Keys**: 9

**Usage Context**:
- Character stat panels
- Equipment stat displays
- Stat comparison screens
- Tooltip labels

**Translation Guidelines**:
- Provide both abbreviation and full name versions
- Maintain consistency across all UI elements
- Consider space constraints in UI (abbreviations max 6 characters)

**Stat Definitions**:
| Key | Full Name | Abbreviation | Description |
|-----|-----------|--------------|-------------|
| `hp` | Health Points | HP | Character survivability |
| `atk` | Attack | ATK | Physical/magical damage output |
| `def` | Defense | DEF | Damage reduction |
| `spd` | Speed | SPD | Turn order priority |
| `crit` | Critical Rate | CRIT | Chance to deal critical hits |
| `critDmg` | Critical Damage | CRIT DMG | Critical hit multiplier |
| `accuracy` | Accuracy | ACC | Hit chance |
| `evasion` | Evasion | EVA | Dodge chance |
| `effectRes` | Effect Resistance | EFF RES | Debuff resistance |

**Example Implementation**:
```csharp
string hpLabel = LocalizationManager.GetText("stats", "hp");
string atkLabel = LocalizationManager.GetText("stats", "atk");
```

---

### 8. Reward Messages
**Category Key**: `rewards`

**Purpose**: Reward notifications and achievement celebration messages.

**Keys Included**:
- Completion: `first_clear`, `quest_complete`
- Periodic: `daily_reward`, `weekly_reward`
- Milestones: `achievement_unlocked`, `level_up`, `new_hero`, `rank_up`

**Total Keys**: 7

**Usage Context**:
- Popup notifications
- Reward collection screens
- Achievement toast messages
- Level up celebrations

**Translation Guidelines**:
- Celebratory, positive tone
- Can include exclamation marks
- Keep brief (max 100 characters)

**Example Implementation**:
```csharp
string levelUpMsg = LocalizationManager.GetText("rewards", "level_up");
string newHeroMsg = LocalizationManager.GetText("rewards", "new_hero");
```

---

### 9. System Notifications
**Category Key**: `notifications`

**Purpose**: Event notifications and system messages for time-sensitive information.

**Keys Included**:
- Maintenance: `maintenance_soon`, `maintenance_active`
- Events: `event_starting`, `event_ending`
- Resources: `energy_full`
- Completion: `quest_complete`
- Social: `friend_request`, `guild_invite`

**Total Keys**: 8

**Dynamic Parameters**: `{time}`, `{eventName}`

**Usage Context**:
- Push notifications
- In-game notification center
- System announcements
- Banner alerts

**Translation Guidelines**:
- Clear, informative tone
- Include specific details (time, event name) via parameters
- Urgent messages should have appropriate urgency markers

**Parameter Usage Examples**:
```csharp
// With time parameter
string maintenanceMsg = LocalizationManager.GetText(
    "notifications",
    "maintenance_soon",
    new Dictionary<string, string> { {"time", "30 minutes"} }
);
// Result: "Server maintenance in 30 minutes"

// With event name parameter
string eventMsg = LocalizationManager.GetText(
    "notifications",
    "event_starting",
    new Dictionary<string, string> { {"eventName", "Winter Festival"} }
);
// Result: "Winter Festival is starting now!"
```

---

### 10. Error Messages
**Category Key**: `errors`

**Purpose**: Error and warning messages for failed operations or invalid states.

**Keys Included**:
- Technical: `network_error`, `server_error`, `invalid_input`
- Gameplay: `not_enough_resources`, `level_too_low`, `feature_locked`

**Total Keys**: 6

**Usage Context**:
- Error popups
- Validation messages
- Failed action feedback
- Requirement warnings

**Translation Guidelines**:
- Clear, helpful explanations
- Avoid technical jargon when possible
- Suggest solutions when applicable
- Neutral, non-blaming tone

**Example Implementation**:
```csharp
string networkError = LocalizationManager.GetText("errors", "network_error");
string notEnoughGold = LocalizationManager.GetText("errors", "not_enough_resources");
```

---

### 11. Battle UI
**Category Key**: `battle`

**Purpose**: Battle screen controls and combat-related messages.

**Keys Included**:
- Results: `victory`, `defeat`
- Controls: `auto`, `pause`, `resume`, `retreat`, `confirm_retreat`
- Speed: `speed_x1`, `speed_x2`, `speed_x3`

**Total Keys**: 10

**Usage Context**:
- Battle HUD
- Battle results screen
- Combat controls
- Speed control toggles

**Translation Guidelines**:
- Short, action-oriented
- All caps acceptable for emphasis (VICTORY, DEFEAT)
- Clear action verbs

**Example Implementation**:
```csharp
string victoryText = LocalizationManager.GetText("battle", "victory");
string autoButtonLabel = LocalizationManager.GetText("battle", "auto");
```

---

### 12. Shop UI
**Category Key**: `shop`

**Purpose**: Shop interface labels and purchase-related messages.

**Keys Included**:
- Actions: `purchase`, `purchase_confirm`
- States: `insufficient_currency`, `purchase_limit`, `sold_out`
- Marketing: `daily_deal`, `best_value`, `limited_time`

**Total Keys**: 8

**Usage Context**:
- Shop screens
- Purchase confirmation dialogs
- Product labels
- Sale banners

**Translation Guidelines**:
- Clear, commercial tone
- Marketing terms should be localized appropriately
- Price and currency handled separately

**Example Implementation**:
```csharp
string purchaseBtn = LocalizationManager.GetText("shop", "purchase");
string bestValue = LocalizationManager.GetText("shop", "best_value");
```

---

### 13. Gacha/Summon UI
**Category Key**: `gacha`

**Purpose**: Summon system interface labels and gacha-related text.

**Keys Included**:
- Actions: `summon`, `summon_x1`, `summon_x10`
- Features: `guaranteed_ssr`, `pity_count`, `rates`, `history`, `skip`

**Total Keys**: 8

**Dynamic Parameters**: `{count}`

**Usage Context**:
- Summon/gacha screens
- Banner displays
- Pity counter displays
- Rate information panels

**Translation Guidelines**:
- Exciting, enticing tone
- Clear numerical information
- Legal compliance for gacha rates display

**Parameter Usage Example**:
```csharp
// Pity counter with dynamic count
string pityText = LocalizationManager.GetText(
    "gacha",
    "pity_count",
    new Dictionary<string, string> { {"count", "45"} }
);
// Result: "45 summons until guaranteed SSR"
```

---

## Summary Section

### `summary` (object)
Provides high-level statistics and metadata about the localization system.

#### `summary.totalCategories` (number)
- **Type**: Integer
- **Description**: Total number of localization categories
- **Example**: `13`

#### `summary.totalKeys` (number)
- **Type**: Integer
- **Description**: Total count of all localization keys across all categories
- **Usage**: Progress tracking, translation completeness metrics
- **Example**: `143`

#### `summary.supportedLanguages` (array of strings)
- **Type**: Array of Language Codes
- **Description**: List of all supported language codes (ISO 639-1)
- **Standard**: ISO 639-1 two-letter codes
- **Example**: `["en", "zh", "ko"]` (English, Chinese, Korean)
- **Usage**: Language selector, validation checks

#### `summary.expansionKeys` (object)
- **Type**: Object mapping category to expansion instructions
- **Description**: Guidelines for adding new keys when content expands
- **Usage**: Developer reference for maintaining key naming conventions

**Expansion Instructions**:
```json
{
  "characters": "New characters require CHAR_NAME_* and CHAR_DESC_* keys",
  "skills": "New skills require SKILL_NAME_* and SKILL_DESC_* keys",
  "items": "New items require ITEM_NAME_* and ITEM_DESC_* keys",
  "gearSets": "New gear sets require SET_NAME_* and SET_DESC_* keys"
}
```

---

## Naming Conventions

### `namingConventions` (object)
Defines the standardized patterns for creating localization keys.

#### `namingConventions.entityNames` (string)
- **Pattern**: `ENTITY_TYPE_NAME_ID`
- **Format**: SCREAMING_SNAKE_CASE (uppercase with underscores)
- **Example**: `CHAR_NAME_FIRE_DRAGON`, `SKILL_NAME_BLIZZARD`
- **Usage**: All entity names (characters, skills, items, sets)

#### `namingConventions.entityDescriptions` (string)
- **Pattern**: `ENTITY_TYPE_DESC_ID`
- **Format**: SCREAMING_SNAKE_CASE (uppercase with underscores)
- **Example**: `CHAR_DESC_FIRE_DRAGON`, `ITEM_DESC_SWORD_FLAME`
- **Usage**: All entity descriptions

#### `namingConventions.uiElements` (string)
- **Pattern**: `snake_case`
- **Format**: lowercase with underscores
- **Example**: `main_menu`, `daily_quests`, `purchase_confirm`
- **Usage**: UI labels, navigation, actions

#### `namingConventions.parameters` (string)
- **Pattern**: `{parameterName}`
- **Format**: camelCase enclosed in curly braces
- **Example**: `{time}`, `{eventName}`, `{count}`
- **Usage**: Dynamic runtime parameters in translations

---

## Best Practices

### `bestPractices` (object)
Guidelines for maintaining translation quality and consistency.

#### `bestPractices.consistency` (string)
- **Guideline**: Use consistent patterns for related content
- **Example**: All character names follow `CHAR_NAME_*` pattern
- **Benefit**: Easier maintenance, predictable key structure

#### `bestPractices.parameters` (string)
- **Guideline**: Use parameterized strings for dynamic content
- **Example**: `{time}`, `{count}`, `{playerName}`
- **Benefit**: Single translation handles multiple cases, reduces key proliferation

#### `bestPractices.fallback` (string)
- **Guideline**: All keys must exist in the fallback language (English)
- **Requirement**: English is the default/fallback language
- **Benefit**: Prevents missing text errors

#### `bestPractices.contextual` (string)
- **Guideline**: Include context in key names to avoid ambiguity
- **Example**: Use `battle_victory` instead of just `victory`
- **Benefit**: Translators understand context, reduces errors

#### `bestPractices.maxLength` (string)
- **Guideline**: Keep translations under 1000 characters as per validation rules
- **Requirement**: Hard limit enforced by validation system
- **Note**: Most UI text should be much shorter (20-100 characters)

---

## Integration Guide

### `integrationGuide` (object)
Technical implementation examples for Unity integration.

#### `integrationGuide.unity` (object)
Unity-specific implementation examples.

##### `unity.example` (string)
Basic usage example:
```csharp
LocalizationManager.GetText("characters", "CHAR_NAME_FIRE_DRAGON")
```

##### `unity.parameters` (string)
Parameterized string example:
```csharp
LocalizationManager.GetText(
    "notifications",
    "maintenance_soon",
    new Dictionary<string, string> {
        {"time", "30 minutes"}
    }
)
```

#### `integrationGuide.configReferences` (object)
Cross-references to other configuration files that reference localization keys.

##### `configReferences.characters` (string)
- **File**: `Characters.json`
- **Fields**: `meta.nameKey`, `meta.descriptionKey`
- **Usage**: Character display text

##### `configReferences.skills` (string)
- **File**: `Skills.json`
- **Fields**: `nameKey`, `descriptionKey`
- **Usage**: Skill display text

##### `configReferences.items` (string)
- **File**: `Items.json`
- **Fields**: `nameKey`, `descriptionKey`
- **Usage**: Item display text

##### `configReferences.gearSets` (string)
- **File**: `GearSets.json`
- **Fields**: `nameKey`, `descriptionKey`
- **Usage**: Gear set display text

---

## Field Naming Conventions Summary

### Entity Pattern (SCREAMING_SNAKE_CASE)
```
ENTITY_TYPE_NAME_IDENTIFIER
ENTITY_TYPE_DESC_IDENTIFIER
```
**Examples**:
- `CHAR_NAME_FIRE_DRAGON`
- `SKILL_DESC_BLIZZARD`
- `ITEM_NAME_HP_POTION`
- `SET_DESC_LIFESTEAL`

### UI Pattern (snake_case)
```
context_action
feature_element
```
**Examples**:
- `main_menu`
- `purchase_confirm`
- `daily_quests`

### Parameter Pattern ({camelCase})
```
{parameterName}
```
**Examples**:
- `{time}`
- `{playerName}`
- `{itemCount}`

---

## Data Validation Rules

1. **Unique Keys**: All keys within a category must be unique
2. **Pattern Compliance**: Keys must follow naming convention for their category
3. **Fallback Existence**: Every key must have an English translation (fallback language)
4. **Length Limits**: Translations must not exceed 1000 characters
5. **Parameter Matching**: Parameterized strings must have matching parameters across all languages
6. **No HTML**: Translations should not contain HTML tags (use rich text markup instead)
7. **Character Encoding**: All files must be UTF-8 encoded

---

## Translation Workflow

### 1. Key Addition Process
1. Developer identifies need for new translatable text
2. Determine appropriate category based on content type
3. Follow naming convention to create key name
4. Add key to `LocalizationKeys.json` in appropriate category
5. Update `totalKeys` count for category
6. Add English text to fallback language file
7. Submit for translation to localization team

### 2. Translation Update Process
1. Translator receives key list for specific language
2. Translator provides translations maintaining parameter placeholders
3. Validation checks ensure:
   - All keys are translated
   - Parameters match source
   - Length limits respected
4. QA reviews translations in-game context
5. Translations deployed to game build

---

## Related Configuration Files

### Core Entity Definitions
- **Characters.json**: References `characters` category via `nameKey`/`descriptionKey`
- **Skills.json**: References `skills` category via `nameKey`/`descriptionKey`
- **Items.json**: References `items` category via `nameKey`/`descriptionKey`
- **GearSets.json**: References `gearSets` category via `nameKey`/`descriptionKey`

### Localization Files
- **Localization_en.json**: English translations (fallback language)
- **Localization_zh.json**: Chinese translations
- **Localization_ko.json**: Korean translations

### Schema Files
- **Localization.schema.md**: Validation rules for translation files

---

## Version History

- **1.0.0** (2025-11-23): Initial localization key mapping documentation
  - 13 categories defined
  - 143 total localization keys
  - 3 supported languages
  - Comprehensive naming conventions established

---

## Maintenance Guidelines

### Adding New Content
When adding new game content (characters, skills, items):
1. **Identify Category**: Match content type to existing category
2. **Follow Pattern**: Use established naming pattern (e.g., `CHAR_NAME_*`)
3. **Add to Keys Array**: Add new keys to appropriate category's `keys` array
4. **Update Count**: Increment `totalKeys` for that category
5. **Update Summary**: Update `summary.totalKeys` with new total
6. **Add Translations**: Ensure fallback (English) translation exists

### Adding New Categories
If new category is needed:
1. Create category object with all required fields
2. Define clear `description` and `pattern` if applicable
3. Add initial keys
4. Update `summary.totalCategories`
5. Document usage context and guidelines
6. Update this schema documentation

### Deprecating Keys
Never delete old keys immediately:
1. Mark key as deprecated in documentation
2. Add deprecation notice in comments
3. Maintain for at least 2 major versions
4. Monitor usage analytics
5. Remove only when usage confirms safe deletion

---

## Common Pitfalls to Avoid

1. **Inconsistent Naming**: Mixing snake_case and SCREAMING_SNAKE_CASE in same category
2. **Missing Fallback**: Adding key without English translation
3. **Hardcoded Text**: Putting translatable text directly in code instead of using keys
4. **Ambiguous Keys**: Generic names like "error" instead of "network_error"
5. **Missing Parameters**: Forgetting to mark dynamic content with `{parameter}` syntax
6. **Over-categorization**: Creating too many narrow categories instead of logical groupings

---

## Support and Documentation

For questions about localization:
- **Key Naming**: Refer to `namingConventions` section
- **Integration**: See `integrationGuide.unity` examples
- **Translation Process**: Review "Translation Workflow" section
- **Validation**: Check `Localization.schema.md` for validation rules

---

**Last Updated**: 2025-11-29
**Document Version**: 1.0.0
**Maintained By**: Localization Team
