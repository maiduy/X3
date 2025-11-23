# Localization Implementation Guide

**Version:** 1.0.0
**Last Updated:** 2025-11-23
**Languages Supported:** English (en), Chinese Simplified (zh), Korean (ko)

---

## Table of Contents

1. [Overview](#overview)
2. [File Structure](#file-structure)
3. [Unity C# Integration](#unity-c-integration)
4. [Usage Examples](#usage-examples)
5. [Adding New Languages](#adding-new-languages)
6. [Adding New Keys](#adding-new-keys)
7. [Format Strings and Parameters](#format-strings-and-parameters)
8. [Best Practices](#best-practices)
9. [Troubleshooting](#troubleshooting)
10. [Translator Guidelines](#translator-guidelines)

---

## Overview

The localization system provides multi-language support for all game text and UI elements. It uses a key-based lookup system with fallback mechanisms to ensure text is always displayed, even if translations are missing.

### Key Features

- **Multi-language Support**: English, Chinese, Korean (Japanese and Vietnamese ready for future activation)
- **Dynamic Loading**: Language files loaded on-demand from CDN
- **Parameterized Strings**: Support for dynamic values like `{time}`, `{count}`, `{eventName}`
- **Format Localization**: Date, time, number, and currency formatting per language
- **Fallback System**: Missing translations automatically fall back to English
- **Hot Reload**: Changes to language files can be applied without restarting
- **Validation**: Built-in checks for missing keys and malformed text

---

## File Structure

```
config/localization/
├── Localization.json           # Master configuration
├── LocalizationKeys.json       # Key mapping and documentation
└── languages/
    ├── en.json                 # English translations
    ├── zh.json                 # Chinese (Simplified) translations
    └── ko.json                 # Korean translations
```

### File Descriptions

**Localization.json**
- Defines supported languages and their metadata
- Configures format rules (date, time, number, currency)
- Sets pluralization rules per language
- Validation settings

**LocalizationKeys.json**
- Documents all localization keys
- Provides key naming conventions
- Integration examples for developers

**languages/{lang}.json**
- Contains actual translated text for each language
- Organized by category (common, characters, skills, items, ui, etc.)
- 143+ keys across 13 categories

---

## Unity C# Integration

### 1. LocalizationManager Class

Create a `LocalizationManager.cs` singleton to handle all localization operations:

```csharp
using System;
using System.Collections.Generic;
using UnityEngine;
using Newtonsoft.Json.Linq;

public class LocalizationManager : MonoBehaviour
{
    public static LocalizationManager Instance { get; private set; }

    private string currentLanguage = "en";
    private Dictionary<string, JObject> loadedLanguages = new Dictionary<string, JObject>();
    private JObject localizationConfig;
    private string fallbackLanguage = "en";

    void Awake()
    {
        if (Instance == null)
        {
            Instance = this;
            DontDestroyOnLoad(gameObject);
            Initialize();
        }
        else
        {
            Destroy(gameObject);
        }
    }

    private void Initialize()
    {
        // Load master localization config
        TextAsset configAsset = Resources.Load<TextAsset>("config/localization/Localization");
        localizationConfig = JObject.Parse(configAsset.text);

        fallbackLanguage = localizationConfig["fallbackLanguage"]?.ToString() ?? "en";

        // Auto-detect system language
        if ((bool)localizationConfig["settings"]["autoDetectLanguage"])
        {
            currentLanguage = DetectSystemLanguage();
        }

        // Load default language
        LoadLanguage(currentLanguage);
        LoadLanguage(fallbackLanguage); // Always load fallback
    }

    private string DetectSystemLanguage()
    {
        SystemLanguage sysLang = Application.systemLanguage;

        switch (sysLang)
        {
            case SystemLanguage.Chinese:
            case SystemLanguage.ChineseSimplified:
                return "zh";
            case SystemLanguage.Korean:
                return "ko";
            case SystemLanguage.Japanese:
                return "ja";
            case SystemLanguage.Vietnamese:
                return "vi";
            default:
                return "en";
        }
    }

    public void LoadLanguage(string languageCode)
    {
        if (loadedLanguages.ContainsKey(languageCode))
            return;

        try
        {
            string path = $"config/localization/languages/{languageCode}";
            TextAsset langAsset = Resources.Load<TextAsset>(path);

            if (langAsset != null)
            {
                JObject langData = JObject.Parse(langAsset.text);
                loadedLanguages[languageCode] = langData;
                Debug.Log($"Loaded language: {languageCode}");
            }
            else
            {
                Debug.LogWarning($"Language file not found: {languageCode}");
            }
        }
        catch (Exception e)
        {
            Debug.LogError($"Failed to load language {languageCode}: {e.Message}");
        }
    }

    public void SetLanguage(string languageCode)
    {
        if (!loadedLanguages.ContainsKey(languageCode))
        {
            LoadLanguage(languageCode);
        }

        if (loadedLanguages.ContainsKey(languageCode))
        {
            currentLanguage = languageCode;
            OnLanguageChanged?.Invoke(languageCode);
        }
    }

    public string GetText(string category, string key, Dictionary<string, string> parameters = null)
    {
        string text = GetRawText(category, key);

        if (parameters != null && parameters.Count > 0)
        {
            foreach (var param in parameters)
            {
                text = text.Replace($"{{{param.Key}}}", param.Value);
            }
        }

        return text;
    }

    private string GetRawText(string category, string key)
    {
        // Try current language
        if (loadedLanguages.TryGetValue(currentLanguage, out JObject currentLang))
        {
            JToken token = currentLang[category]?[key];
            if (token != null)
                return token.ToString();
        }

        // Fallback to default language
        if (currentLanguage != fallbackLanguage &&
            loadedLanguages.TryGetValue(fallbackLanguage, out JObject fallback))
        {
            JToken token = fallback[category]?[key];
            if (token != null)
            {
                Debug.LogWarning($"Missing translation: {category}.{key} in {currentLanguage}, using fallback");
                return token.ToString();
            }
        }

        // Last resort: return the key itself
        Debug.LogError($"Missing localization key: {category}.{key}");
        return $"[{category}.{key}]";
    }

    public string FormatDate(DateTime date)
    {
        string format = localizationConfig["formats"]?["date"]?[currentLanguage]?.ToString()
                        ?? "MM/DD/YYYY";

        // Convert format string to C# format
        format = format.Replace("YYYY", "yyyy").Replace("MM", "MM").Replace("DD", "dd");
        format = format.Replace("年", "年").Replace("月", "月").Replace("日", "日");
        format = format.Replace("년", "년").Replace("월", "월").Replace("일", "일");

        return date.ToString(format);
    }

    public string FormatNumber(double number)
    {
        var numberFormat = localizationConfig["formats"]?["number"]?[currentLanguage];
        string decimalSep = numberFormat?["decimal"]?.ToString() ?? ".";
        string thousandsSep = numberFormat?["thousands"]?.ToString() ?? ",";

        // Format with thousands separator
        return number.ToString($"#,##0.##").Replace(",", thousandsSep).Replace(".", decimalSep);
    }

    public string FormatCurrency(double amount)
    {
        var currencyFormat = localizationConfig["formats"]?["currency"]?[currentLanguage];
        string symbol = currencyFormat?["symbol"]?.ToString() ?? "$";
        string position = currencyFormat?["position"]?.ToString() ?? "before";

        string formatted = FormatNumber(amount);

        return position == "before" ? $"{symbol}{formatted}" : $"{formatted}{symbol}";
    }

    public string GetCurrentLanguage() => currentLanguage;
    public string[] GetSupportedLanguages()
    {
        var languages = localizationConfig["supportedLanguages"] as JArray;
        List<string> codes = new List<string>();

        foreach (var lang in languages)
        {
            if ((bool)lang["enabled"])
            {
                codes.Add(lang["code"].ToString());
            }
        }

        return codes.ToArray();
    }

    public event Action<string> OnLanguageChanged;
}
```

### 2. LocalizedText Component

Create a `LocalizedText.cs` component for UI text elements:

```csharp
using UnityEngine;
using UnityEngine.UI;
using TMPro;

public class LocalizedText : MonoBehaviour
{
    [SerializeField] private string category;
    [SerializeField] private string key;
    [SerializeField] private bool useTextMeshPro = true;

    private Text uiText;
    private TextMeshProUGUI tmpText;

    void Awake()
    {
        if (useTextMeshPro)
            tmpText = GetComponent<TextMeshProUGUI>();
        else
            uiText = GetComponent<Text>();
    }

    void OnEnable()
    {
        UpdateText();
        LocalizationManager.Instance.OnLanguageChanged += OnLanguageChanged;
    }

    void OnDisable()
    {
        if (LocalizationManager.Instance != null)
            LocalizationManager.Instance.OnLanguageChanged -= OnLanguageChanged;
    }

    private void OnLanguageChanged(string newLanguage)
    {
        UpdateText();
    }

    private void UpdateText()
    {
        string localizedText = LocalizationManager.Instance.GetText(category, key);

        if (useTextMeshPro && tmpText != null)
            tmpText.text = localizedText;
        else if (uiText != null)
            uiText.text = localizedText;
    }

    public void SetKey(string newCategory, string newKey)
    {
        category = newCategory;
        key = newKey;
        UpdateText();
    }
}
```

---

## Usage Examples

### Basic Text Lookup

```csharp
// Get simple text
string okText = LocalizationManager.Instance.GetText("common", "ok");
// Returns: "OK" (en), "确定" (zh), "확인" (ko)

string firedragonName = LocalizationManager.Instance.GetText("characters", "CHAR_NAME_FIRE_DRAGON");
// Returns: "Fire Dragon" (en), "火焰之龙" (zh), "화염 드래곤" (ko)
```

### Parameterized Strings

```csharp
// Text with dynamic parameters
var parameters = new Dictionary<string, string>
{
    { "time", "30 minutes" },
};

string maintenanceMsg = LocalizationManager.Instance.GetText(
    "notifications",
    "maintenance_soon",
    parameters
);
// Returns: "Server maintenance will begin in 30 minutes." (en)
// Returns: "服务器将在30 minutes后进入维护。" (zh)
// Returns: "서버 점검이 30 minutes 후에 시작됩니다." (ko)
```

### Format Numbers and Currency

```csharp
// Format numbers
double gold = 1234567.89;
string formattedGold = LocalizationManager.Instance.FormatNumber(gold);
// Returns: "1,234,567.89" (en, zh, ko all use same format)

// Format currency
double price = 9.99;
string formattedPrice = LocalizationManager.Instance.FormatCurrency(price);
// Returns: "$9.99" (en), "¥9.99" (zh), "9.99₩" (ko)
```

### Format Dates

```csharp
DateTime now = DateTime.Now;
string formattedDate = LocalizationManager.Instance.FormatDate(now);
// Returns: "11/23/2025" (en)
// Returns: "2025年11月23日" (zh)
// Returns: "2025년 11월 23일" (ko)
```

### Using LocalizedText Component

```csharp
// Attach LocalizedText component to UI Text/TextMeshPro
// Set category = "common", key = "ok" in inspector
// Text automatically updates when language changes

// Change text dynamically
LocalizedText localizedText = GetComponent<LocalizedText>();
localizedText.SetKey("battle", "victory");
```

### Character/Item Display

```csharp
// Display character name from config
string characterId = "CHAR_FIRE_DRAGON";
string nameKey = $"CHAR_NAME_{characterId.Replace("CHAR_", "")}";
string descKey = $"CHAR_DESC_{characterId.Replace("CHAR_", "")}";

string name = LocalizationManager.Instance.GetText("characters", nameKey);
string desc = LocalizationManager.Instance.GetText("characters", descKey);

Debug.Log($"{name}: {desc}");
```

---

## Adding New Languages

### Step 1: Create Language File

1. Copy `en.json` as a template
2. Rename to `{language_code}.json` (e.g., `ja.json` for Japanese)
3. Translate all values (keep keys unchanged!)

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "version": "1.0.0",
  "language": "ja",
  "lastUpdated": "2025-11-23T00:00:00Z",
  "common": {
    "ok": "OK",
    "cancel": "キャンセル",
    "confirm": "確認",
    ...
  }
}
```

### Step 2: Update Localization.json

Add language definition to `supportedLanguages` array:

```json
{
  "code": "ja",
  "name": "Japanese",
  "nativeName": "日本語",
  "direction": "ltr",
  "enabled": true,
  "completeness": 1.0,
  "localeCode": "ja-JP"
}
```

Add format configurations:

```json
"formats": {
  "date": {
    "ja": "YYYY年MM月DD日"
  },
  "time": {
    "ja": "24h"
  },
  "number": {
    "ja": {"decimal": ".", "thousands": ","}
  },
  "currency": {
    "ja": {"symbol": "¥", "position": "before"}
  }
}
```

### Step 3: Update Manifest.json

Add new language file entry:

```json
"languageJa": {
  "path": "localization/languages/ja.json",
  "hash": "generated_hash_here",
  "size": 5000,
  "priority": 6,
  "critical": false,
  "description": "Japanese language translations"
}
```

### Step 4: Update SystemSettings.json

Add language code to supported languages:

```json
"supportedLanguages": ["en", "zh", "ko", "ja"]
```

---

## Adding New Keys

### Step 1: Choose Category

Determine which category your new key belongs to:
- `common` - Basic UI buttons and labels
- `characters` - Character names/descriptions
- `skills` - Skill names/descriptions
- `items` - Item names/descriptions
- `ui` - Navigation and menus
- `errors` - Error messages
- etc.

### Step 2: Follow Naming Convention

**Entity Names/Descriptions** (uppercase with underscores):
```
CHAR_NAME_FIRE_DRAGON
CHAR_DESC_FIRE_DRAGON
SKILL_NAME_PHOENIX_STRIKE
ITEM_NAME_SWORD_FLAME
```

**UI Elements** (snake_case):
```
main_menu
daily_quests
purchase_confirm
```

### Step 3: Add to All Language Files

Add the key with appropriate translations to **ALL** language files:

**en.json:**
```json
"characters": {
  "CHAR_NAME_WATER_SPIRIT": "Water Spirit",
  "CHAR_DESC_WATER_SPIRIT": "A mystical being born from ocean depths."
}
```

**zh.json:**
```json
"characters": {
  "CHAR_NAME_WATER_SPIRIT": "水之精灵",
  "CHAR_DESC_WATER_SPIRIT": "诞生于海洋深处的神秘存在。"
}
```

**ko.json:**
```json
"characters": {
  "CHAR_NAME_WATER_SPIRIT": "물의 정령",
  "CHAR_DESC_WATER_SPIRIT": "바다 깊은 곳에서 태어난 신비한 존재."
}
```

### Step 4: Update LocalizationKeys.json

Document the new key in the mapping file:

```json
"characters": {
  "keys": [
    ...existing keys...,
    "CHAR_NAME_WATER_SPIRIT", "CHAR_DESC_WATER_SPIRIT"
  ],
  "totalKeys": 12
}
```

---

## Format Strings and Parameters

### Parameter Syntax

Use `{parameterName}` in curly braces for dynamic values:

```json
"maintenance_soon": "Server maintenance will begin in {time}.",
"event_ending": "Event '{eventName}' will end in {time}.",
"pity_count": "Pity: {count}/90"
```

### Usage in Code

```csharp
var params = new Dictionary<string, string>
{
    { "eventName", "Dragon Raid" },
    { "time", "2 hours" }
};

string text = LocalizationManager.Instance.GetText("notifications", "event_ending", params);
```

### Pluralization

For languages with complex plural rules, use the pluralization configuration:

```json
"pluralizationRules": {
  "en": {
    "one": "{count} item",
    "other": "{count} items"
  },
  "zh": {
    "other": "{count}个物品"
  }
}
```

---

## Best Practices

### For Developers

1. **Always Use Keys, Never Hardcode Text**
   ```csharp
   // BAD
   buttonText.text = "OK";

   // GOOD
   buttonText.text = LocalizationManager.Instance.GetText("common", "ok");
   ```

2. **Use LocalizedText Components for Static Text**
   - Attach to UI elements in the editor
   - Automatically updates on language change

3. **Keep Keys Contextual**
   ```csharp
   // BAD - Ambiguous
   "name"

   // GOOD - Clear context
   "CHAR_NAME_FIRE_DRAGON"
   ```

4. **Don't Concatenate Strings**
   ```csharp
   // BAD - Word order varies by language
   text = "You have " + count + " items";

   // GOOD - Use parameters
   text = GetText("inventory", "item_count", new Dictionary<string, string> { {"count", count.ToString()} });
   ```

5. **Test with All Languages**
   - Different languages have different text lengths
   - Design UI to accommodate longer translations (German, Russian can be 30% longer)

### For Translators

1. **Preserve Parameters**
   - Never translate text inside `{curly braces}`
   - Parameters like `{time}`, `{count}`, `{eventName}` must remain exactly as written

2. **Consider Context**
   - Use key names and descriptions to understand context
   - Ask developers if meaning is unclear

3. **Maintain Tone**
   - Match the tone of the original (formal, casual, dramatic, etc.)
   - Stay consistent with game's voice

4. **Test in Game**
   - View translations in actual UI
   - Check for text overflow, truncation, wrapping

5. **Cultural Adaptation**
   - Adapt idioms and expressions appropriately
   - Use culturally appropriate examples

---

## Troubleshooting

### Problem: Text Shows as `[category.key]`

**Cause:** Missing localization key

**Solution:**
1. Check if key exists in language file
2. Check spelling of category and key
3. Ensure language file is loaded correctly
4. Check console for error messages

### Problem: Wrong Language Displays

**Cause:** Language detection or loading issue

**Solution:**
```csharp
Debug.Log($"Current Language: {LocalizationManager.Instance.GetCurrentLanguage()}");
Debug.Log($"Supported Languages: {string.Join(", ", LocalizationManager.Instance.GetSupportedLanguages())}");
```

### Problem: Parameters Not Replaced

**Cause:** Parameter dictionary not passed or wrong parameter names

**Solution:**
1. Verify parameter names match exactly (case-sensitive)
2. Check dictionary is not null
3. Ensure parameters are passed to GetText method

### Problem: Special Characters Display Incorrectly

**Cause:** Encoding issue

**Solution:**
- Ensure all JSON files saved with UTF-8 encoding
- Check Unity import settings for text assets
- Verify font includes necessary glyphs for target language

### Problem: Text Doesn't Update When Language Changes

**Cause:** Component not subscribed to OnLanguageChanged event

**Solution:**
```csharp
void OnEnable()
{
    LocalizationManager.Instance.OnLanguageChanged += OnLanguageChanged;
}

void OnDisable()
{
    LocalizationManager.Instance.OnLanguageChanged -= OnLanguageChanged;
}

void OnLanguageChanged(string newLanguage)
{
    // Update your text here
}
```

---

## Translator Guidelines

### Translation Workflow

1. **Receive Source Files**
   - English JSON file (en.json)
   - LocalizationKeys.json for context

2. **Translation Process**
   - Translate all string values
   - Keep all keys unchanged
   - Preserve parameters `{like_this}`
   - Maintain JSON structure

3. **Quality Check**
   - Verify JSON syntax (use online validator)
   - Check for missing keys
   - Test in-game if possible

4. **Delivery**
   - Submit translated JSON file
   - Note any questions or ambiguities
   - Flag culturally sensitive content

### Key Naming Reference

| Pattern | Example | Description |
|---------|---------|-------------|
| `CHAR_NAME_*` | `CHAR_NAME_FIRE_DRAGON` | Character names |
| `CHAR_DESC_*` | `CHAR_DESC_FIRE_DRAGON` | Character descriptions |
| `SKILL_NAME_*` | `SKILL_NAME_PHOENIX_STRIKE` | Skill names |
| `SKILL_DESC_*` | `SKILL_DESC_PHOENIX_STRIKE` | Skill descriptions |
| `ITEM_NAME_*` | `ITEM_NAME_SWORD_FLAME` | Item names |
| `ITEM_DESC_*` | `ITEM_DESC_SWORD_FLAME` | Item descriptions |
| `SET_NAME_*` | `SET_NAME_INFERNO` | Gear set names |
| `SET_DESC_*` | `SET_DESC_INFERNO` | Gear set descriptions |

### Common Parameters

| Parameter | Description | Example |
|-----------|-------------|---------|
| `{time}` | Time duration | "30 minutes", "2 hours" |
| `{count}` | Numeric count | "5", "90" |
| `{eventName}` | Event name | "Dragon Raid", "Season Pass" |
| `{itemName}` | Item name | "Health Potion", "Flame Sword" |
| `{playerName}` | Player name | Variable user input |

---

## Performance Considerations

### Language File Loading

- Language files loaded on-demand
- Fallback language (English) always loaded
- Consider lazy loading for mobile to reduce initial load time

### Caching

```csharp
// Localization config enables caching
"cacheEnabled": true,
"cacheDuration": 86400  // 24 hours
```

### Hot Reload

For development, enable hot reload to see changes without restart:

```csharp
// In SystemSettings.json
"hotReloadEnabled": true
```

### Memory Usage

- Each language file: ~5-8 KB
- All 3 languages: ~20 KB total
- Negligible memory impact

---

## Version Control

### Localization Version Tracking

Track localization version separately from app version:

```json
"languagePackVersion": "1.0.0"
```

Increment when:
- Adding new keys
- Updating translations
- Changing language support

### Manifest Integration

Localization files included in Manifest.json for:
- Hash verification
- Incremental updates
- Rollback capability

---

## Future Enhancements

### Planned Features

1. **Voice-Over Support**
   - Audio file references per language
   - Lip-sync timing data

2. **Gender-Specific Translations**
   - Support languages with gendered nouns
   - Player character gender affects text

3. **Regional Variants**
   - zh-CN (Simplified Chinese)
   - zh-TW (Traditional Chinese)
   - en-US vs en-GB

4. **Translation Memory**
   - Reuse common translations
   - Consistency across updates

5. **Automated QA**
   - Missing key detection
   - Parameter validation
   - Length limit warnings

---

## Support and Resources

### Internal Resources

- Config directory: `config/localization/`
- Documentation: `CONFIG_FINAL_STATUS.txt`
- Key reference: `LocalizationKeys.json`

### External Tools

- JSON Validator: https://jsonlint.com/
- Unicode Character Lookup: https://unicode-table.com/
- Translation Memory: Use CAT tools like Crowdin, Lokalise

### Contact

For localization questions:
- Technical issues: Engineering team
- Translation questions: Localization team
- New language requests: Product team

---

**End of Localization Implementation Guide**
