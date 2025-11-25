# Localization.json Schema Documentation

## Overview
The Localization configuration defines the master localization system for the game, including supported languages, language file management, format customization, pluralization rules, and validation settings.

## Version History
- **v1.0** (Initial): Core localization system with 5 language support
- **v1.1**: Added hot reload and compression support
- **v1.2**: Enhanced pluralization rules for additional languages

---

## Root Level Structure

### Complete Structure
```json
{
  "defaultLanguage": "en",
  "supportedLanguages": [...],
  "languageFiles": {...},
  "settings": {...},
  "formats": {...},
  "pluralizationRules": {...},
  "validation": {...}
}
```

---

## Field Descriptions

### 1. `defaultLanguage`
- **Type**: `string`
- **Format**: ISO 639-1 language code (2 letters)
- **Description**: The default fallback language used when a player's preferred language is unavailable or when translations are missing
- **Valid Values**: Must match one of the `supportedLanguages` codes
- **Default**: `"en"`

**Example:**
```json
"defaultLanguage": "en"
```

**Usage:**
- Applied when device language is not supported
- Used as fallback when translations are missing in other languages
- First language loaded on app initialization

---

### 2. `supportedLanguages`
- **Type**: `array` of `SupportedLanguage` objects
- **Description**: Array defining all languages available in the game, including metadata and availability status

#### SupportedLanguage Object Structure:
```json
{
  "code": "en",
  "name": "English",
  "nativeName": "English",
  "direction": "ltr",
  "enabled": true,
  "completeness": 100,
  "localeCode": "en-US"
}
```

#### SupportedLanguage Fields:

##### `code`
- **Type**: `string`
- **Format**: ISO 639-1 language code
- **Description**: Unique identifier for the language
- **Valid Values**: `"en"`, `"zh"`, `"ko"`, `"ja"`, `"vi"`

##### `name`
- **Type**: `string`
- **Description**: English name of the language
- **Examples**: `"English"`, `"Chinese"`, `"Korean"`, `"Japanese"`, `"Vietnamese"`

##### `nativeName`
- **Type**: `string`
- **Description**: Language name in its native script
- **Examples**:
  - `"English"` (English)
  - `"中文"` (Chinese)
  - `"한국어"` (Korean)
  - `"日本語"` (Japanese)
  - `"Tiếng Việt"` (Vietnamese)

##### `direction`
- **Type**: `string`
- **Valid Values**: `"ltr"` (left-to-right), `"rtl"` (right-to-left)
- **Description**: Text reading direction
- **Default**: `"ltr"` for all currently supported languages
- **Note**: RTL support (e.g., Arabic) would require UI layout adjustments

##### `enabled`
- **Type**: `boolean`
- **Description**: Whether the language is currently available to players
- **Usage**: Can disable languages during translation work or for specific regions

##### `completeness`
- **Type**: `number`
- **Format**: Percentage (0-100)
- **Description**: Translation completion percentage
- **Calculation**: `(translatedKeys / totalKeys) × 100`
- **Thresholds**:
  - `100%`: Fully translated, recommended for production
  - `95-99%`: Nearly complete, acceptable for release
  - `80-94%`: Partially complete, suitable for beta testing
  - `<80%`: Not recommended for release

##### `localeCode`
- **Type**: `string`
- **Format**: BCP 47 language tag (e.g., `"en-US"`, `"zh-CN"`)
- **Description**: Full locale identifier including region
- **Purpose**: Used for system-level formatting (dates, numbers, currency)

**Example Array:**
```json
"supportedLanguages": [
  {
    "code": "en",
    "name": "English",
    "nativeName": "English",
    "direction": "ltr",
    "enabled": true,
    "completeness": 100,
    "localeCode": "en-US"
  },
  {
    "code": "zh",
    "name": "Chinese",
    "nativeName": "中文",
    "direction": "ltr",
    "enabled": true,
    "completeness": 100,
    "localeCode": "zh-CN"
  },
  {
    "code": "ko",
    "name": "Korean",
    "nativeName": "한국어",
    "direction": "ltr",
    "enabled": true,
    "completeness": 98,
    "localeCode": "ko-KR"
  },
  {
    "code": "ja",
    "name": "Japanese",
    "nativeName": "日本語",
    "direction": "ltr",
    "enabled": false,
    "completeness": 75,
    "localeCode": "ja-JP"
  },
  {
    "code": "vi",
    "name": "Vietnamese",
    "nativeName": "Tiếng Việt",
    "direction": "ltr",
    "enabled": false,
    "completeness": 60,
    "localeCode": "vi-VN"
  }
]
```

---

### 3. `languageFiles`
- **Type**: `object`
- **Description**: Defines the file structure, paths, and metadata for language resource files

#### Structure:
```json
{
  "baseDirectory": "Localization/",
  "fileNamingPattern": "{languageCode}.json",
  "languages": {
    "en": {...},
    "zh": {...},
    "ko": {...}
  }
}
```

#### Fields:

##### `baseDirectory`
- **Type**: `string`
- **Description**: Root directory for language files relative to the game's resource folder
- **Default**: `"Localization/"`
- **Note**: Must end with `/`

##### `fileNamingPattern`
- **Type**: `string`
- **Description**: Template for language file names
- **Placeholder**: `{languageCode}` replaced with the language code
- **Example**: Pattern `"{languageCode}.json"` generates:
  - `en.json`
  - `zh.json`
  - `ko.json`

##### `languages` (Object)
- **Type**: `object`
- **Description**: Metadata for each language file
- **Keys**: Language codes (`"en"`, `"zh"`, `"ko"`, etc.)

**Language File Metadata Structure:**
```json
"en": {
  "path": "Localization/en.json",
  "hash": "a3f5b7c9d1e2f4g6h8i0j2k4",
  "size": 45632,
  "lastModified": "2025-03-15T10:30:00Z"
}
```

###### Metadata Fields:

**`path`**
- **Type**: `string`
- **Description**: Full path to the language file
- **Format**: Combines `baseDirectory` + `fileNamingPattern`

**`hash`**
- **Type**: `string`
- **Format**: MD5 or SHA-256 hash
- **Description**: File integrity checksum
- **Purpose**:
  - Verify file integrity after download
  - Detect file changes for hot reload
  - Cache invalidation

**`size`**
- **Type**: `integer`
- **Unit**: Bytes
- **Description**: File size in bytes
- **Purpose**:
  - Download progress calculation
  - Storage requirement estimation
  - Network bandwidth planning

**`lastModified`**
- **Type**: `string`
- **Format**: ISO 8601 timestamp
- **Description**: Last modification timestamp
- **Purpose**:
  - Version tracking
  - Hot reload trigger
  - Translation update detection

**Example:**
```json
"languageFiles": {
  "baseDirectory": "Localization/",
  "fileNamingPattern": "{languageCode}.json",
  "languages": {
    "en": {
      "path": "Localization/en.json",
      "hash": "a3f5b7c9d1e2f4g6h8i0j2k4",
      "size": 45632,
      "lastModified": "2025-03-15T10:30:00Z"
    },
    "zh": {
      "path": "Localization/zh.json",
      "hash": "b2c4d6e8f0g2h4i6j8k0l2m4",
      "size": 52108,
      "lastModified": "2025-03-14T14:20:00Z"
    },
    "ko": {
      "path": "Localization/ko.json",
      "hash": "c1d3e5f7g9h1i3j5k7l9m1n3",
      "size": 48976,
      "lastModified": "2025-03-13T09:15:00Z"
    }
  }
}
```

---

### 4. `settings`
- **Type**: `object`
- **Description**: Configuration for localization system behavior

#### Structure:
```json
{
  "autoDetectLanguage": true,
  "fallbackLanguage": "en",
  "caching": {...},
  "hotReload": {...},
  "compression": {...}
}
```

#### Fields:

##### `autoDetectLanguage`
- **Type**: `boolean`
- **Description**: Automatically detect and use device/system language on first launch
- **Default**: `true`
- **Behavior**:
  - `true`: Use device language if supported, else use `defaultLanguage`
  - `false`: Always use `defaultLanguage` unless player manually changes

##### `fallbackLanguage`
- **Type**: `string`
- **Description**: Language to use when a translation key is missing
- **Default**: `"en"`
- **Must**: Match a `supportedLanguages` code

##### `caching` (Object)
- **Type**: `object`
- **Description**: Language file caching configuration

**Structure:**
```json
"caching": {
  "enabled": true,
  "strategy": "memory",
  "ttl": 3600
}
```

**Fields:**
- `enabled` (boolean): Enable/disable caching
- `strategy` (string): Cache storage method
  - `"memory"`: In-memory cache (fast, cleared on restart)
  - `"disk"`: Persistent disk cache
  - `"hybrid"`: Memory + disk fallback
- `ttl` (integer): Time-to-live in seconds (3600 = 1 hour)

##### `hotReload` (Object)
- **Type**: `object`
- **Description**: Configuration for reloading translations without app restart

**Structure:**
```json
"hotReload": {
  "enabled": true,
  "checkInterval": 300
}
```

**Fields:**
- `enabled` (boolean): Enable/disable hot reload feature
- `checkInterval` (integer): Seconds between file change checks
  - Recommended: 300 (5 minutes) for production
  - Development: 30-60 seconds

**Use Cases:**
- Live translation updates during development
- Emergency text fixes without app update
- A/B testing different translations

##### `compression` (Object)
- **Type**: `object`
- **Description**: Language file compression settings

**Structure:**
```json
"compression": {
  "enabled": true,
  "algorithm": "gzip",
  "level": 6
}
```

**Fields:**
- `enabled` (boolean): Enable/disable compression
- `algorithm` (string): Compression algorithm
  - `"gzip"`: Standard GZIP (widely supported)
  - `"brotli"`: Brotli (better compression, modern browsers)
  - `"lz4"`: LZ4 (faster decompression, larger files)
- `level` (integer): Compression level (1-9)
  - `1-3`: Fast compression, larger files
  - `4-6`: Balanced (recommended)
  - `7-9`: Maximum compression, slower

**File Size Impact:**
- Uncompressed: ~45 KB (English)
- GZIP level 6: ~12 KB (73% reduction)
- Brotli level 6: ~10 KB (78% reduction)

**Complete Example:**
```json
"settings": {
  "autoDetectLanguage": true,
  "fallbackLanguage": "en",
  "caching": {
    "enabled": true,
    "strategy": "memory",
    "ttl": 3600
  },
  "hotReload": {
    "enabled": true,
    "checkInterval": 300
  },
  "compression": {
    "enabled": true,
    "algorithm": "gzip",
    "level": 6
  }
}
```

---

### 5. `formats`
- **Type**: `object`
- **Description**: Locale-specific formatting rules for dates, times, numbers, and currency
- **Purpose**: Ensures culturally appropriate display formats for each language

#### Structure:
```json
{
  "en": {...},
  "zh": {...},
  "ko": {...}
}
```

#### Format Configuration Object:
```json
{
  "date": "MM/DD/YYYY",
  "time": "hh:mm A",
  "number": "1,234.56",
  "currency": "$#,##0.00"
}
```

#### Fields:

##### `date`
- **Type**: `string`
- **Description**: Date format pattern
- **Tokens**:
  - `YYYY`: 4-digit year (2025)
  - `YY`: 2-digit year (25)
  - `MM`: 2-digit month (01-12)
  - `M`: Month without leading zero (1-12)
  - `DD`: 2-digit day (01-31)
  - `D`: Day without leading zero (1-31)

**Examples:**
- `"MM/DD/YYYY"`: 03/15/2025 (US format)
- `"YYYY-MM-DD"`: 2025-03-15 (ISO format, used in China/Korea)
- `"DD.MM.YYYY"`: 15.03.2025 (European format)
- `"YYYY年MM月DD日"`: 2025年03月15日 (Japanese format with kanji)

##### `time`
- **Type**: `string`
- **Description**: Time format pattern
- **Tokens**:
  - `HH`: 24-hour format (00-23)
  - `hh`: 12-hour format (01-12)
  - `mm`: Minutes (00-59)
  - `ss`: Seconds (00-59)
  - `A`: AM/PM marker
  - `a`: am/pm marker (lowercase)

**Examples:**
- `"hh:mm A"`: 02:30 PM (12-hour with AM/PM)
- `"HH:mm"`: 14:30 (24-hour format)
- `"HH:mm:ss"`: 14:30:45 (with seconds)
- `"A hh:mm"`: PM 02:30 (Korean style, AM/PM first)

##### `number`
- **Type**: `string`
- **Description**: Number formatting pattern showing thousand separators and decimal points
- **Components**:
  - Thousands separator: `,` (comma) or `.` (period) or ` ` (space)
  - Decimal separator: `.` (period) or `,` (comma)

**Examples:**
- `"1,234.56"`: English format (comma thousands, period decimal)
- `"1 234,56"`: French format (space thousands, comma decimal)
- `"1.234,56"`: German format (period thousands, comma decimal)
- `"1,234.56"`: Standard format for all Asian languages in this config

**Real Number Examples:**
| Format Pattern | Display of 1234567.89 |
|----------------|----------------------|
| `"1,234.56"`   | 1,234,567.89        |
| `"1 234,56"`   | 1 234 567,89        |
| `"1.234,56"`   | 1.234.567,89        |

##### `currency`
- **Type**: `string`
- **Description**: Currency format pattern with symbol placement
- **Components**:
  - Currency symbol: `$`, `¥`, `₩`, `₫`, etc.
  - `#`: Digit placeholder
  - `,`: Thousands separator
  - `.`: Decimal separator
  - `0`: Zero placeholder (forces display even if zero)

**Examples:**
- `"$#,##0.00"`: $1,234.56 (USD, symbol before, 2 decimals)
- `"¥#,##0"`: ¥1,235 (CNY/JPY, symbol before, no decimals)
- `"₩#,##0"`: ₩1,235 (KRW, symbol before, no decimals)
- `"#,##0.00₫"`: 1,234.56₫ (VND, symbol after)
- `"#,##0.00 EUR"`: 1,234.56 EUR (Euro with code)

**Currency Display Examples:**
| Format Pattern  | Display of 1234.56 |
|-----------------|-------------------|
| `"$#,##0.00"`   | $1,234.56        |
| `"¥#,##0"`      | ¥1,235           |
| `"₩#,##0"`      | ₩1,235           |
| `"#,##0₫"`      | 1,235₫           |

#### Complete Format Examples by Language:

**English (en-US):**
```json
"en": {
  "date": "MM/DD/YYYY",
  "time": "hh:mm A",
  "number": "1,234.56",
  "currency": "$#,##0.00"
}
```
- Date: 03/15/2025
- Time: 02:30 PM
- Number: 1,234,567.89
- Currency: $1,234.56

**Chinese (zh-CN):**
```json
"zh": {
  "date": "YYYY-MM-DD",
  "time": "HH:mm",
  "number": "1,234.56",
  "currency": "¥#,##0.00"
}
```
- Date: 2025-03-15
- Time: 14:30
- Number: 1,234,567.89
- Currency: ¥1,234.56

**Korean (ko-KR):**
```json
"ko": {
  "date": "YYYY-MM-DD",
  "time": "A hh:mm",
  "number": "1,234.56",
  "currency": "₩#,##0"
}
```
- Date: 2025-03-15
- Time: 오후 02:30 (PM 02:30)
- Number: 1,234,567.89
- Currency: ₩1,235 (rounded, no decimals)

**Complete Example:**
```json
"formats": {
  "en": {
    "date": "MM/DD/YYYY",
    "time": "hh:mm A",
    "number": "1,234.56",
    "currency": "$#,##0.00"
  },
  "zh": {
    "date": "YYYY-MM-DD",
    "time": "HH:mm",
    "number": "1,234.56",
    "currency": "¥#,##0.00"
  },
  "ko": {
    "date": "YYYY-MM-DD",
    "time": "A hh:mm",
    "number": "1,234.56",
    "currency": "₩#,##0"
  }
}
```

---

### 6. `pluralizationRules`
- **Type**: `object`
- **Description**: Language-specific rules for handling plural forms of nouns
- **Purpose**: Different languages have different pluralization rules (English has 2 forms, Russian has 3, Arabic has 6)

#### Structure:
```json
{
  "en": {...},
  "zh": {...},
  "ko": {...}
}
```

#### Pluralization Rule Object:
```json
{
  "rule": "english",
  "forms": 2,
  "examples": [...]
}
```

#### Fields:

##### `rule`
- **Type**: `string`
- **Description**: Named pluralization rule set
- **Common Rules**:
  - `"english"`: 2 forms (singular/plural)
  - `"chinese"`: 1 form (no pluralization)
  - `"korean"`: 1 form (no pluralization)
  - `"japanese"`: 1 form (no pluralization)
  - `"french"`: 2 forms (0/1 vs 2+)
  - `"russian"`: 3 forms (complex rules)
  - `"arabic"`: 6 forms (very complex)

##### `forms`
- **Type**: `integer`
- **Description**: Number of plural forms for the language
- **Range**: 1-6

**Form Count by Language:**
- **1 form**: Chinese, Japanese, Korean, Vietnamese (no grammatical plurals)
- **2 forms**: English, Spanish, Italian, German
- **3 forms**: Russian, Polish, Croatian
- **4 forms**: Slovenian
- **6 forms**: Arabic

##### `examples`
- **Type**: `array` of `string`
- **Description**: Example translations demonstrating each plural form
- **Format**: Array length matches `forms` value

#### Pluralization Logic Examples:

**English (2 forms):**
```json
"en": {
  "rule": "english",
  "forms": 2,
  "examples": [
    "{count} item",
    "{count} items"
  ]
}
```
**Rules:**
- Form 0 (singular): n == 1 → "1 item"
- Form 1 (plural): n != 1 → "0 items", "2 items", "5 items"

**Usage in Code:**
```javascript
// count = 1 → "1 item"
// count = 0 → "0 items"
// count = 5 → "5 items"
const text = pluralize(count, "items");
```

**Chinese/Korean/Japanese (1 form):**
```json
"zh": {
  "rule": "chinese",
  "forms": 1,
  "examples": [
    "{count} 个物品"
  ]
}
```
**Rules:**
- Form 0 (no distinction): all counts use same form
- "1 个物品", "5 个物品" (same pattern)

**Korean Example:**
```json
"ko": {
  "rule": "korean",
  "forms": 1,
  "examples": [
    "{count}개 아이템"
  ]
}
```
- "1개 아이템", "5개 아이템" (same pattern)

#### Advanced Pluralization (Reference):

**Russian (3 forms):**
```json
"ru": {
  "rule": "russian",
  "forms": 3,
  "examples": [
    "{count} предмет",    // 1, 21, 31, 41, ... (ends in 1, not 11)
    "{count} предмета",   // 2-4, 22-24, 32-34, ... (ends in 2-4, not 12-14)
    "{count} предметов"   // 0, 5-20, 25-30, ... (everything else)
  ]
}
```

**Rules:**
- Form 0: n % 10 == 1 && n % 100 != 11
- Form 1: n % 10 in [2,3,4] && n % 100 not in [12,13,14]
- Form 2: everything else

**Examples:**
- 1 предмет, 21 предмет, 101 предмет
- 2 предмета, 3 предмета, 22 предмета
- 0 предметов, 5 предметов, 11 предметов, 20 предметов

**Arabic (6 forms):**
```json
"ar": {
  "rule": "arabic",
  "forms": 6,
  "examples": [
    "لا عناصر",          // 0 (zero)
    "عنصر واحد",         // 1 (singular)
    "عنصران",            // 2 (dual)
    "{count} عناصر",     // 3-10 (few)
    "{count} عنصرًا",    // 11-99 (many)
    "{count} عنصر"       // 100+ (other)
  ]
}
```

#### Complete Example:
```json
"pluralizationRules": {
  "en": {
    "rule": "english",
    "forms": 2,
    "examples": [
      "{count} item",
      "{count} items"
    ]
  },
  "zh": {
    "rule": "chinese",
    "forms": 1,
    "examples": [
      "{count} 个物品"
    ]
  },
  "ko": {
    "rule": "korean",
    "forms": 1,
    "examples": [
      "{count}개 아이템"
    ]
  }
}
```

#### Implementation in Localization Files:

**en.json:**
```json
{
  "items_count": [
    "{count} item",
    "{count} items"
  ]
}
```

**zh.json:**
```json
{
  "items_count": [
    "{count} 个物品"
  ]
}
```

**Usage:**
```javascript
// English: 1 item, 5 items
// Chinese: 1 个物品, 5 个物品
// Korean: 1개 아이템, 5개 아이템
const text = LocalizationManager.getPluralText("items_count", count);
```

---

### 7. `validation`
- **Type**: `object`
- **Description**: Validation rules and constraints for localization content

#### Structure:
```json
{
  "requireAllKeys": true,
  "warnMissingKeys": true,
  "allowHTMLTags": false,
  "maxTextLength": 500
}
```

#### Fields:

##### `requireAllKeys`
- **Type**: `boolean`
- **Description**: Enforce that all languages must have all translation keys
- **Default**: `true`
- **Behavior**:
  - `true`: Build/validation fails if any language is missing keys
  - `false`: Missing keys use fallback language
- **Recommended**: `true` for production, `false` during active translation

##### `warnMissingKeys`
- **Type**: `boolean`
- **Description**: Log warnings when translation keys are missing
- **Default**: `true`
- **Output**: Console warnings during runtime
- **Format**: `[Localization] Missing key 'character_name_001' for language 'ko', using fallback 'en'`

##### `allowHTMLTags`
- **Type**: `boolean`
- **Description**: Allow HTML/rich text tags in translation strings
- **Default**: `false` (security best practice)
- **Use Cases**:
  - `false`: Plain text only (recommended)
  - `true`: Allow formatted text with tags like `<color=#FF0000>`, `<b>`, `<i>`

**Examples:**
```json
// allowHTMLTags: false
"skill_description": "Deals 150% ATK as Fire damage"

// allowHTMLTags: true
"skill_description": "Deals <color=#FF0000>150%</color> <b>ATK</b> as <color=#FF5500>Fire</color> damage"
```

**Security Note:** If enabled, must sanitize/validate tags to prevent XSS attacks.

##### `maxTextLength`
- **Type**: `integer`
- **Unit**: Characters
- **Description**: Maximum allowed character count for any translation string
- **Default**: `500`
- **Purpose**:
  - Prevent UI overflow
  - Ensure readability
  - Maintain consistent UX across languages

**Length Guidelines by Context:**
| Context | Max Length | Example |
|---------|-----------|---------|
| Character names | 30 | "Aria, Blade of Dawn" |
| Skill names | 40 | "Inferno Strike: Phoenix Rising" |
| Skill descriptions | 200 | Full damage formula + effects |
| Item descriptions | 300 | Lore + stats + set bonus info |
| Quest titles | 60 | "Chapter 5: The Forgotten Kingdom" |
| Quest descriptions | 500 | Full quest briefing |
| Error messages | 150 | Clear, actionable message |
| Button labels | 20 | "Confirm", "Cancel" |

**Validation Behavior:**
- If `maxTextLength` exceeded: Log error/warning
- Build-time: Fail validation (if strict mode)
- Runtime: Truncate with ellipsis "..." or show error

**Complete Example:**
```json
"validation": {
  "requireAllKeys": true,
  "warnMissingKeys": true,
  "allowHTMLTags": false,
  "maxTextLength": 500
}
```

---

## Data Validation Rules

### Language Code Validation
```javascript
// Must be ISO 639-1 (2 letters, lowercase)
const isValidLanguageCode = /^[a-z]{2}$/.test(code);
```

### Locale Code Validation
```javascript
// Must be BCP 47 format (language-COUNTRY)
const isValidLocaleCode = /^[a-z]{2}-[A-Z]{2}$/.test(localeCode);
// Examples: en-US, zh-CN, ko-KR, ja-JP, vi-VN
```

### Completeness Validation
```javascript
// Must be 0-100 percentage
if (completeness < 0 || completeness > 100) {
  throw new Error("Completeness must be between 0 and 100");
}
```

### File Hash Validation
```javascript
// Should be valid MD5 (32 chars) or SHA-256 (64 chars)
const isValidHash = /^[a-f0-9]{32}$|^[a-f0-9]{64}$/.test(hash);
```

### Consistency Checks

1. **Default Language Must Be Supported:**
```javascript
const supportedCodes = supportedLanguages.map(lang => lang.code);
if (!supportedCodes.includes(defaultLanguage)) {
  throw new Error("defaultLanguage must be in supportedLanguages");
}
```

2. **Fallback Language Must Be Supported:**
```javascript
if (!supportedCodes.includes(settings.fallbackLanguage)) {
  throw new Error("fallbackLanguage must be in supportedLanguages");
}
```

3. **Language Files Must Match Supported Languages:**
```javascript
const fileLanguages = Object.keys(languageFiles.languages);
const enabledLanguages = supportedLanguages
  .filter(lang => lang.enabled)
  .map(lang => lang.code);

// All enabled languages must have file entries
for (const lang of enabledLanguages) {
  if (!fileLanguages.includes(lang)) {
    throw new Error(`Missing language file for enabled language: ${lang}`);
  }
}
```

4. **Format Definitions Must Match Supported Languages:**
```javascript
for (const lang of supportedCodes) {
  if (!formats[lang]) {
    throw new Error(`Missing format definition for language: ${lang}`);
  }
}
```

5. **Pluralization Rules Must Match Supported Languages:**
```javascript
for (const lang of supportedCodes) {
  if (!pluralizationRules[lang]) {
    throw new Error(`Missing pluralization rules for language: ${lang}`);
  }
}
```

### File Size Limits
```javascript
// Recommended limits per language file
const MAX_FILE_SIZE = 5 * 1024 * 1024; // 5 MB
const WARN_FILE_SIZE = 1 * 1024 * 1024; // 1 MB

if (languageFile.size > MAX_FILE_SIZE) {
  throw new Error("Language file exceeds maximum size (5MB)");
} else if (languageFile.size > WARN_FILE_SIZE) {
  console.warn("Language file size exceeds 1MB, consider splitting");
}
```

---

## Design Guidelines

### Adding New Languages

1. **Update `supportedLanguages` array:**
```json
{
  "code": "fr",
  "name": "French",
  "nativeName": "Français",
  "direction": "ltr",
  "enabled": false,
  "completeness": 0,
  "localeCode": "fr-FR"
}
```

2. **Create language file entry:**
```json
"languageFiles": {
  "languages": {
    "fr": {
      "path": "Localization/fr.json",
      "hash": "NEW_FILE_HASH",
      "size": 0,
      "lastModified": "2025-03-20T10:00:00Z"
    }
  }
}
```

3. **Add format definitions:**
```json
"formats": {
  "fr": {
    "date": "DD/MM/YYYY",
    "time": "HH:mm",
    "number": "1 234,56",
    "currency": "#,##0.00 €"
  }
}
```

4. **Add pluralization rules:**
```json
"pluralizationRules": {
  "fr": {
    "rule": "french",
    "forms": 2,
    "examples": [
      "{count} objet",
      "{count} objets"
    ]
  }
}
```

5. **Create actual language file `fr.json`** with all translation keys

6. **Test and update completeness percentage**

7. **Set `enabled: true` when ready for release**

### Disabling Languages

To temporarily disable a language (e.g., during major translation updates):

```json
{
  "code": "ja",
  "enabled": false,
  "completeness": 75
}
```

Players won't see this language in settings, but you can keep it in the config for internal testing.

### Managing Translation Updates

**Workflow:**
1. Update translation keys in language files (e.g., `en.json`)
2. Recalculate file hash and size
3. Update `lastModified` timestamp
4. Update `completeness` percentage for affected languages
5. If hot reload enabled: System auto-detects changes and reloads

**Hash Recalculation:**
```bash
# MD5
md5sum Localization/en.json

# SHA-256
sha256sum Localization/en.json
```

### Performance Optimization

**Compression Recommendations:**
- **Production**: `gzip` level 6 (best compatibility)
- **Modern platforms**: `brotli` level 6 (better compression)
- **Low-end devices**: `lz4` (faster decompression)

**Caching Strategy:**
- **Mobile**: `memory` cache (saves disk space)
- **Desktop/Console**: `hybrid` cache (faster subsequent loads)
- **Web**: Browser cache with proper headers

**File Size Guidelines:**
- Keep individual language files under 1 MB
- If exceeding 1 MB, consider splitting into:
  - Core strings (UI, common)
  - Story/lore strings (loaded on demand)
  - Event-specific strings (loaded per event)

### Multi-Region Support

For region-specific variants (e.g., en-US vs en-GB, zh-CN vs zh-TW):

**Option 1: Separate Languages**
```json
{
  "code": "en-us",
  "name": "English (US)",
  "localeCode": "en-US"
},
{
  "code": "en-gb",
  "name": "English (UK)",
  "localeCode": "en-GB"
}
```

**Option 2: Variant System (Not Currently Implemented)**
```json
{
  "code": "en",
  "variants": [
    {"region": "US", "localeCode": "en-US"},
    {"region": "GB", "localeCode": "en-GB"}
  ]
}
```

### Text Length Management

**Prevention:**
1. Set appropriate `maxTextLength` per content type
2. Provide length guidelines to translators
3. Test all languages in UI mockups

**Handling Overflow:**
- Short text (buttons): Abbreviate or use icons
- Medium text (descriptions): Ellipsis "..."
- Long text (quest descriptions): Scrollable containers

**Language-Specific Considerations:**
- **Chinese/Japanese**: Characters represent more meaning, tend to be shorter
- **German**: Compound words can be very long, need more space
- **English**: Generally medium length
- **Korean**: Tends to be slightly longer than English

### RTL Language Support

To add right-to-left languages (Arabic, Hebrew):

```json
{
  "code": "ar",
  "name": "Arabic",
  "nativeName": "العربية",
  "direction": "rtl",
  "enabled": true,
  "completeness": 100,
  "localeCode": "ar-SA"
}
```

**Required UI Adjustments:**
- Mirror entire UI layout
- Flip text alignment (right-aligned by default)
- Reverse icon positions
- Adjust animation directions
- Test with actual RTL text (not just flipped LTR)

---

## Usage Examples

### Example 1: First Launch Language Detection
```javascript
function determineInitialLanguage() {
  const config = LocalizationConfig;

  if (!config.settings.autoDetectLanguage) {
    return config.defaultLanguage;
  }

  const deviceLanguage = getDeviceLanguage(); // e.g., "ko"
  const supportedLang = config.supportedLanguages.find(
    lang => lang.code === deviceLanguage && lang.enabled
  );

  if (supportedLang) {
    return supportedLang.code;
  }

  return config.defaultLanguage;
}

// Result: If device is Korean → "ko", else → "en"
```

### Example 2: Loading Language File with Caching
```javascript
async function loadLanguageFile(languageCode) {
  const config = LocalizationConfig;
  const fileInfo = config.languageFiles.languages[languageCode];

  if (!fileInfo) {
    throw new Error(`No file info for language: ${languageCode}`);
  }

  // Check cache first
  if (config.settings.caching.enabled) {
    const cached = await getFromCache(fileInfo.path, fileInfo.hash);
    if (cached) {
      console.log(`Loaded ${languageCode} from cache`);
      return cached;
    }
  }

  // Download file
  const response = await fetch(fileInfo.path);
  const compressed = await response.arrayBuffer();

  // Verify integrity
  const downloadedHash = await calculateHash(compressed);
  if (downloadedHash !== fileInfo.hash) {
    throw new Error(`File integrity check failed for ${languageCode}`);
  }

  // Decompress
  let content;
  if (config.settings.compression.enabled) {
    content = await decompress(compressed, config.settings.compression.algorithm);
  } else {
    content = compressed;
  }

  const translations = JSON.parse(content);

  // Cache for future use
  if (config.settings.caching.enabled) {
    await saveToCache(fileInfo.path, fileInfo.hash, translations, config.settings.caching.ttl);
  }

  return translations;
}
```

### Example 3: Formatting Currency
```javascript
function formatCurrency(amount, languageCode) {
  const config = LocalizationConfig;
  const format = config.formats[languageCode];

  if (!format) {
    languageCode = config.settings.fallbackLanguage;
    format = config.formats[languageCode];
  }

  // Parse currency format pattern
  const pattern = format.currency; // e.g., "$#,##0.00" or "¥#,##0"
  const symbolBefore = pattern.startsWith('$') || pattern.startsWith('¥') || pattern.startsWith('₩');
  const symbol = pattern.match(/[$¥₩₫€£]/)?.[0] || '$';
  const decimals = pattern.includes('.') ? 2 : 0;

  // Format number
  const formatted = new Intl.NumberFormat(
    config.supportedLanguages.find(l => l.code === languageCode)?.localeCode,
    {
      minimumFractionDigits: decimals,
      maximumFractionDigits: decimals
    }
  ).format(amount);

  // Apply symbol position
  if (symbolBefore) {
    return `${symbol}${formatted}`;
  } else {
    return `${formatted}${symbol}`;
  }
}

// Examples:
formatCurrency(1234.56, "en") // "$1,234.56"
formatCurrency(1234.56, "zh") // "¥1,234.56"
formatCurrency(1234.56, "ko") // "₩1,235"
```

### Example 4: Handling Pluralization
```javascript
function getPlural(key, count, languageCode) {
  const config = LocalizationConfig;
  const translations = getCurrentTranslations(languageCode);
  const rule = config.pluralizationRules[languageCode];

  if (!translations[key]) {
    console.warn(`Missing translation key: ${key}`);
    return `[${key}]`;
  }

  // Get correct plural form index
  let formIndex = 0;
  if (rule.rule === "english") {
    formIndex = count === 1 ? 0 : 1;
  } else if (rule.rule === "chinese" || rule.rule === "korean") {
    formIndex = 0; // Only one form
  } else if (rule.rule === "russian") {
    // Complex Russian rules
    if (count % 10 === 1 && count % 100 !== 11) {
      formIndex = 0;
    } else if ([2,3,4].includes(count % 10) && ![12,13,14].includes(count % 100)) {
      formIndex = 1;
    } else {
      formIndex = 2;
    }
  }

  // Get translation string
  const forms = translations[key];
  const text = Array.isArray(forms) ? forms[formIndex] : forms;

  // Replace {count} placeholder
  return text.replace("{count}", count);
}

// Examples:
getPlural("items_count", 1, "en")  // "1 item"
getPlural("items_count", 5, "en")  // "5 items"
getPlural("items_count", 5, "zh")  // "5 个物品"
getPlural("items_count", 5, "ko")  // "5개 아이템"
```

### Example 5: Hot Reload Implementation
```javascript
class LocalizationHotReload {
  constructor(config) {
    this.config = config;
    this.fileHashes = {};
    this.isEnabled = config.settings.hotReload.enabled;
    this.checkInterval = config.settings.hotReload.checkInterval * 1000; // Convert to ms

    if (this.isEnabled) {
      this.startMonitoring();
    }
  }

  startMonitoring() {
    setInterval(() => this.checkForUpdates(), this.checkInterval);
  }

  async checkForUpdates() {
    for (const [langCode, fileInfo] of Object.entries(this.config.languageFiles.languages)) {
      const currentHash = this.fileHashes[langCode];

      if (currentHash && currentHash !== fileInfo.hash) {
        console.log(`[HotReload] Detected change in ${langCode}.json`);
        await this.reloadLanguage(langCode);
      }

      this.fileHashes[langCode] = fileInfo.hash;
    }
  }

  async reloadLanguage(langCode) {
    try {
      const newTranslations = await loadLanguageFile(langCode);
      updateTranslations(langCode, newTranslations);

      // Trigger UI refresh for currently displayed language
      if (getCurrentLanguage() === langCode) {
        refreshAllLocalizedText();
      }

      console.log(`[HotReload] Successfully reloaded ${langCode}`);
    } catch (error) {
      console.error(`[HotReload] Failed to reload ${langCode}:`, error);
    }
  }
}

// Initialize
const hotReload = new LocalizationHotReload(LocalizationConfig);
```

### Example 6: Validation on Build
```javascript
function validateLocalization(config) {
  const errors = [];
  const warnings = [];

  // Check default language is supported
  const supportedCodes = config.supportedLanguages.map(l => l.code);
  if (!supportedCodes.includes(config.defaultLanguage)) {
    errors.push("defaultLanguage must be in supportedLanguages");
  }

  // Check fallback language is supported
  if (!supportedCodes.includes(config.settings.fallbackLanguage)) {
    errors.push("fallbackLanguage must be in supportedLanguages");
  }

  // Check enabled languages have file entries
  const enabledLanguages = config.supportedLanguages.filter(l => l.enabled);
  for (const lang of enabledLanguages) {
    if (!config.languageFiles.languages[lang.code]) {
      errors.push(`Missing language file entry for enabled language: ${lang.code}`);
    }

    if (!config.formats[lang.code]) {
      errors.push(`Missing format definition for language: ${lang.code}`);
    }

    if (!config.pluralizationRules[lang.code]) {
      errors.push(`Missing pluralization rules for language: ${lang.code}`);
    }
  }

  // Check completeness
  for (const lang of enabledLanguages) {
    if (lang.completeness < 95) {
      warnings.push(`Language ${lang.code} is only ${lang.completeness}% complete`);
    }
  }

  // Check file sizes
  for (const [langCode, fileInfo] of Object.entries(config.languageFiles.languages)) {
    if (fileInfo.size > 5 * 1024 * 1024) {
      errors.push(`Language file ${langCode} exceeds 5MB limit`);
    } else if (fileInfo.size > 1 * 1024 * 1024) {
      warnings.push(`Language file ${langCode} exceeds 1MB, consider splitting`);
    }
  }

  // Check validation rules
  if (config.validation.requireAllKeys) {
    // Load all language files and compare keys
    const allKeys = new Set();
    const languageKeys = {};

    for (const lang of enabledLanguages) {
      const translations = loadLanguageFileSync(lang.code);
      const keys = Object.keys(translations);
      languageKeys[lang.code] = new Set(keys);
      keys.forEach(key => allKeys.add(key));
    }

    // Check for missing keys
    for (const [langCode, keys] of Object.entries(languageKeys)) {
      for (const requiredKey of allKeys) {
        if (!keys.has(requiredKey)) {
          errors.push(`Missing key '${requiredKey}' in language ${langCode}`);
        }
      }
    }
  }

  return { errors, warnings };
}

// Run validation
const { errors, warnings } = validateLocalization(LocalizationConfig);

if (errors.length > 0) {
  console.error("Localization validation failed:");
  errors.forEach(err => console.error(`  - ${err}`));
  process.exit(1);
}

if (warnings.length > 0) {
  console.warn("Localization validation warnings:");
  warnings.forEach(warn => console.warn(`  - ${warn}`));
}
```

---

## Related Configuration Files

### Primary References:
- **LocalizationKeys.json** - Defines all translation key mappings and naming conventions

### Related Language Files:
- **en.json** - English translation strings
- **zh.json** - Chinese translation strings
- **ko.json** - Korean translation strings
- **ja.json** - Japanese translation strings (disabled)
- **vi.json** - Vietnamese translation strings (disabled)

### Configuration Dependencies:
- **SystemSettings.json** - May reference `defaultLanguage` for system-wide settings
- **SegmentConfig.json** - May use language/region for player segmentation
- **Analytics.json** - Tracks language distribution and adoption metrics

### Related Systems:
- **Manifest.json** - Includes language files in download priority
- **FeatureFlags.json** - Can enable/disable specific languages via feature flags

---

## Version History

### v1.0 (Initial Release)
- Core localization system
- Support for English, Chinese, Korean
- Basic caching and compression
- Date/time/number/currency formatting
- Pluralization rules

### v1.1 (Hot Reload Update)
- Added hot reload functionality
- Improved caching strategies (memory/disk/hybrid)
- Enhanced file integrity validation
- Compression algorithm options (gzip/brotli/lz4)

### v1.2 (Pluralization Enhancement)
- Extended pluralization rules
- Support for complex plural forms (Russian, Arabic)
- Improved plural form examples
- Better documentation for translators

### v1.3 (Planned)
- Japanese and Vietnamese language enablement
- RTL language support (Arabic)
- Variant system for regional differences (en-US vs en-GB)
- Split large language files into modules
- Context-aware translations (same key, different translations based on context)

---

## Best Practices Summary

1. **Always define fallback language** (`settings.fallbackLanguage`)
2. **Keep language files under 1 MB** - Split if necessary
3. **Use compression in production** - Reduces download size by 70-80%
4. **Enable caching** - Significantly improves load times
5. **Set `requireAllKeys: true` before release** - Ensures complete translations
6. **Test all languages in UI** - Verify text doesn't overflow
7. **Provide length guidelines to translators** - Prevents UI issues
8. **Use hot reload during development** - Faster iteration on translations
9. **Validate hashes after download** - Ensures file integrity
10. **Monitor completeness percentages** - Track translation progress
11. **Use proper locale codes** - Ensures correct system-level formatting
12. **Follow pluralization rules** - Different languages have different rules
13. **Test with actual devices/languages** - Emulators may not reflect real behavior
14. **Document custom format patterns** - Helps translators understand requirements
15. **Keep consistent naming conventions** - Refer to LocalizationKeys.json

---

## Common Issues and Solutions

### Issue: Missing translations showing "[key_name]"
**Cause:** Key doesn't exist in current language file
**Solution:**
- Check `completeness` percentage
- Verify `requireAllKeys` setting
- Add missing key or use fallback language

### Issue: Text overflowing UI elements
**Cause:** Translation longer than expected
**Solution:**
- Set appropriate `maxTextLength`
- Provide length guidelines to translators
- Use responsive UI containers

### Issue: Wrong date/currency format
**Cause:** Incorrect locale code or format pattern
**Solution:**
- Verify `localeCode` matches language region
- Check format pattern syntax
- Test with actual numbers/dates

### Issue: Hot reload not working
**Cause:** Hash not updated or interval too long
**Solution:**
- Update file hash after translation changes
- Reduce `checkInterval` for faster detection
- Verify `hotReload.enabled` is `true`

### Issue: High memory usage
**Cause:** All languages loaded simultaneously
**Solution:**
- Use `memory` caching strategy
- Only load active language
- Unload unused languages

### Issue: Slow initial load
**Cause:** Large uncompressed files
**Solution:**
- Enable compression (`gzip` level 6)
- Use `disk` or `hybrid` caching
- Preload language on splash screen

---

## Integration Notes

### Unity Integration
```csharp
public class LocalizationManager : MonoBehaviour {
    private Dictionary<string, Dictionary<string, string>> translations;
    private LocalizationConfig config;

    void Start() {
        config = LoadLocalizationConfig();
        string language = DetermineInitialLanguage();
        LoadLanguage(language);
    }

    public string GetText(string key) {
        string currentLang = PlayerPrefs.GetString("Language", config.defaultLanguage);

        if (translations[currentLang].TryGetValue(key, out string text)) {
            return text;
        }

        // Fallback
        if (config.validation.warnMissingKeys) {
            Debug.LogWarning($"Missing key '{key}' for language '{currentLang}'");
        }

        return translations[config.settings.fallbackLanguage][key];
    }
}
```

### Unreal Engine Integration
```cpp
class FLocalizationManager {
    TMap<FString, TSharedPtr<FJsonObject>> Translations;
    FLocalizationConfig Config;

    FText GetText(const FString& Key) {
        FString CurrentLanguage = GetCurrentLanguage();

        if (Translations[CurrentLanguage]->HasField(Key)) {
            return FText::FromString(Translations[CurrentLanguage]->GetStringField(Key));
        }

        // Fallback
        if (Config.Validation.bWarnMissingKeys) {
            UE_LOG(LogLocalization, Warning, TEXT("Missing key '%s' for language '%s'"), *Key, *CurrentLanguage);
        }

        return FText::FromString(Translations[Config.Settings.FallbackLanguage]->GetStringField(Key));
    }
};
```

---

## Summary

The Localization.json configuration provides a comprehensive internationalization system supporting multiple languages with:

- **5 languages** (3 enabled, 2 in progress)
- **Automatic language detection** based on device settings
- **Fallback system** for missing translations
- **Custom formatting** for dates, times, numbers, and currency per language
- **Pluralization rules** handling different grammatical plural forms
- **File integrity validation** using hashes
- **Compression** reducing file sizes by 70-80%
- **Caching strategies** for optimal performance
- **Hot reload** for live translation updates
- **Validation rules** ensuring translation completeness

This system ensures a localized experience for players worldwide while maintaining data integrity, performance, and ease of translation management.
