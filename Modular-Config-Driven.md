-----

### I. NHÓM CORE ENTITY (Thực thể cốt lõi)

#### 1\. `Characters.json`

Định nghĩa dữ liệu tĩnh của nhân vật.

  * **Điểm nhấn:** Tách biệt `stats` (chỉ số) và `assets` (tài nguyên tải) để tối ưu bộ nhớ. `growthCurveId` giúp tái sử dụng công thức tăng trưởng.

<!-- end list -->

```json
[
  {
    "id": "HERO_ARTHUR_01",
    "meta": {
      "nameKey": "CHAR_NAME_ARTHUR",
      "rarity": 5,
      "role": "Warrior",
      "element": "Fire",
      "tags": ["Human", "Melee", "Knight"]
    },
    "assets": {
      "model": "Assets/Chars/Arthur/Model_Base.prefab", // Addressable Key
      "icon": "Assets/UI/Icons/Arthur_Avatar.png",
      "voicePack": "Audio/Voices/Arthur_EN"
    },
    "baseStats": {
      "hp": 1200,
      "atk": 150,
      "def": 80,
      "spd": 105,
      "critRate": 0.15,
      "critDmg": 1.50
    },
    "growth": {
      "curveId": "CURVE_WARRIOR_S_TIER", // Tham chiếu file GrowthCurves.json
      "maxLevel": 60
    },
    "skills": ["SKILL_SWORD_SLASH", "SKILL_HOLY_SHIELD", "SKILL_EXCALIBUR"]
  }
]
```

#### 2\. `Items.json`

Định nghĩa trang bị (Gear) và vật phẩm tiêu hao.

  * **Điểm nhấn:** `statPool` định nghĩa khoảng ngẫu nhiên (RNG) cho chỉ số phụ, quan trọng cho tính năng "đập đồ".

<!-- end list -->

```json
[
  {
    "id": "GEAR_SWORD_DRAGON",
    "type": "Equipment",
    "slot": "Weapon",
    "setBonusId": "SET_LIFESTEAL", // Tham chiếu GearSets.json
    "tier": 6,
    "mainStat": {
      "type": "FlatAttack",
      "base": 100,
      "growth": 15 // Tăng mỗi level cường hóa
    },
    "subStatPool": [ // Các dòng phụ có thể xuất hiện
      { "type": "PercentAttack", "min": 0.05, "max": 0.08 },
      { "type": "CritRate", "min": 0.03, "max": 0.06 },
      { "type": "Speed", "min": 2, "max": 5 }
    ]
  },
  {
    "id": "ITEM_POTION_HP_L",
    "type": "Consumable",
    "effect": { "type": "Heal", "value": 500 }
  }
]
```

-----

### II. NHÓM GAMEPLAY (Cơ chế & Logic)

#### 3\. `Skills.json`

"Trái tim" của combat. Mọi chiêu thức đều được cấu hình tại đây.

  * **Điểm nhấn:** `timeline` đồng bộ hóa Animation và Logic (Sát thương gây ra đúng lúc kiếm chém xuống).

<!-- end list -->

```json
[
  {
    "id": "SKILL_FIREBALL",
    "meta": { "name": "Fireball", "cd": 5.0, "cost": 30 },
    "targeting": {
      "mode": "Unit",
      "filter": { "team": "Enemy", "priority": "LowestHP" },
      "range": 10
    },
    "timeline": [
      { "time": 0.0, "action": "PlayAnim", "param": "Cast_Magic" },
      { "time": 0.3, "action": "SpawnProjectile", "param": "VFX_Fireball_Missile", "speed": 15 },
      { 
        "time": "OnHit", // Sự kiện khi đạn chạm mục tiêu
        "action": "ApplyEffect", 
        "payload": [
          { "type": "Damage", "formula": "caster.atk * 2.5", "element": "Fire" },
          { "type": "ApplyStatus", "id": "Burn", "chance": 0.5, "duration": 2 }
        ]
      }
    ]
  }
]
```

#### 4\. `BattleRules.json`

Định nghĩa luật chơi chung và hằng số Game Design.

  * **Điểm nhấn:** `elementalMatrix` giúp cân bằng hệ khắc chế mà không sửa code.

<!-- end list -->

```json
{
  "constants": {
    "maxDefenseMitigation": 0.85, // Giáp giảm tối đa 85% dmg
    "baseCritDamage": 1.5,
    "speedDivisor": 1000 // Dùng tính thanh hành động (Action Bar)
  },
  "elementalMatrix": {
    "Fire": { "Water": 0.5, "Wind": 1.5, "Earth": 1.0 },
    "Water": { "Fire": 1.5, "Wind": 1.0, "Electric": 0.8 }
  },
  "statusRules": {
    "maxDebuffs": 10,
    "ccHierarchy": ["Stun", "Sleep", "Root"] // Cái trước đè cái sau
  }
}
```

#### 5\. `AIProfiles.json`

Cấu hình trí tuệ nhân tạo (Behavior Tree đơn giản hóa).

  * **Điểm nhấn:** Hệ thống ưu tiên (`priority`) giúp AI ra quyết định thông minh.

<!-- end list -->

```json
{
  "profileId": "AI_HEALER_DEFENSIVE",
  "description": "Healer tập trung cứu đồng đội thấp máu",
  "behaviors": [
    {
      "priority": 100,
      "condition": { "type": "AllyHpBelow", "value": 0.3 },
      "action": { "type": "CastSkill", "tag": "Heal_Ultimate" }
    },
    {
      "priority": 80,
      "condition": { "type": "SelfHpBelow", "value": 0.5 },
      "action": { "type": "CastSkill", "tag": "Heal_Self" }
    },
    {
      "priority": 10,
      "condition": { "type": "Always" },
      "action": { "type": "CastSkill", "tag": "Basic_Attack" }
    }
  ]
}
```

-----

### III. NHÓM CONTENT (Nội dung PvE/PvP)

#### 6\. `Levels.json` (Campaign)

Cấu trúc ải và quái vật.

  * **Điểm nhấn:** `waves` cho phép tạo nhiều đợt quái. `drops` định nghĩa phần thưởng rơi.

<!-- end list -->

```json
[
  {
    "stageId": "STAGE_1_5",
    "chapterId": "CH_1",
    "meta": { "name": "Goblin Camp", "staminaCost": 5, "recPower": 5000 },
    "mapId": "MAP_FOREST_01",
    "waves": [
      {
        "waveIdx": 1,
        "enemies": [
          { "id": "MOB_GOBLIN_A", "level": 10, "pos": 1 },
          { "id": "MOB_GOBLIN_B", "level": 10, "pos": 2 }
        ]
      },
      {
        "waveIdx": 2,
        "enemies": [
          { "id": "BOSS_GOBLIN_CHIEF", "level": 12, "pos": 1, "isBoss": true }
        ]
      }
    ],
    "rewards": {
      "firstClear": [{ "id": "GEM", "qty": 50 }],
      "normalDrop": [{ "id": "GOLD", "min": 100, "max": 200, "rate": 1.0 }]
    }
  }
]
```

#### 7\. `ArenaConfig.json` (PvP)

Cấu hình xếp hạng và ghép trận.

```json
{
  "season": {
    "currentId": "SEASON_2025_Q1",
    "endDate": "2025-03-31T23:59:59Z",
    "resetRule": "SoftReset" // Giảm 1 bậc rank khi qua mùa mới
  },
  "ranks": [
    { "id": "BRONZE", "minElo": 0, "maxElo": 999, "lossProtect": true },
    { "id": "SILVER", "minElo": 1000, "maxElo": 1499, "lossProtect": false },
    { "id": "GOLD", "minElo": 1500, "maxElo": 1999 }
  ],
  "matchmaking": {
    "baseRange": 100, // Tìm đối thủ +/- 100 ELO
    "expandRate": 50, // Mở rộng 50 ELO mỗi 5 giây
    "maxRange": 500
  }
}
```

-----

### IV. NHÓM ECONOMY (Kinh tế)

#### 8\. `Banners.json` (Gacha)

Kiểm soát việc quay tướng.

  * **Điểm nhấn:** Cơ chế `pity` (bảo hiểm) chi tiết và `pickup` (tăng tỉ lệ) nằm hoàn toàn ở Server config.

<!-- end list -->

```json
[
  {
    "id": "BANNER_NEW_YEAR_FIRE",
    "type": "Limited",
    "activeTime": { "start": "2025-01-01", "end": "2025-01-15" },
    "cost": { "item": "SUMMON_SCROLL", "qty": 1 },
    "rates": {
      "SSR": 0.03, // 3%
      "SR": 0.17,
      "R": 0.80
    },
    "pity": {
      "softStart": 50, // Từ lần 50 tỷ lệ tăng dần
      "hardCap": 80,   // Chắc chắn ra SSR ở lần 80
      "resetOnSSR": true
    },
    "pickup": {
      "targetId": "HERO_DRAGON_LORD",
      "shareOfRarity": 0.5 // Chiếm 50% tỷ lệ khi ra SSR
    }
  }
]
```

#### 9\. `ShopProducts.json`

Cửa hàng và gói nạp.

```json
[
  {
    "sku": "IAP_PACK_STARTER",
    "category": "Bundle",
    "price": { "currency": "USD", "amount": 4.99 },
    "content": [
      { "id": "GEM", "qty": 500 },
      { "id": "HERO_HEALER_SR", "qty": 1 }
    ],
    "limits": {
      "type": "Lifetime", // Chỉ mua 1 lần duy nhất
      "max": 1
    },
    "display": { "badge": "BestValue", "priority": 1 }
  },
  {
    "sku": "SHOP_DAILY_STAMINA",
    "category": "Resource",
    "price": { "currency": "GEM", "amount": 50 },
    "content": [{ "id": "STAMINA", "qty": 60 }],
    "limits": { "type": "Daily", "max": 3 }
  }
]
```

-----

### V. NHÓM LIVEOPS (Vận hành)

#### 10\. `EventSchedule.json`

Lịch trình sự kiện toàn cục.

  * **Điểm nhấn:** `modifiers` cho phép thay đổi luật game tạm thời (Buff toàn server).

<!-- end list -->

```json
[
  {
    "eventId": "EVENT_WEEKEND_GOLD",
    "schedule": { "type": "Recurring", "cron": "0 0 * * SAT,SUN" }, // Mỗi thứ 7, CN
    "modifiers": [
      { "target": "DropRate_Gold", "op": "Multiply", "value": 2.0 }, // x2 Vàng
      { "target": "StaminaCost", "op": "Multiply", "value": 0.8 }    // Giảm 20% Sta
    ],
    "ui": { "bannerUrl": "Banners/Event_Weekend_Gold.png" }
  }
]
```

#### 11\. `Quests.json`

Hệ thống nhiệm vụ dựa trên sự kiện (Event-Driven).

```json
[
  {
    "id": "QUEST_DAILY_KILL_BOSS",
    "group": "Daily",
    "resetTime": "00:00:00",
    "description": "Tiêu diệt 3 Boss bất kỳ",
    "listeners": [
      {
        "event": "OnUnitKilled", // Lắng nghe sự kiện Unit chết
        "conditions": {
          "isBoss": true,       // Unit phải là Boss
          "levelMin": 5
        },
        "targetCount": 3
      }
    ],
    "rewards": [
      { "id": "EXP_PASS", "qty": 100 },
      { "id": "GOLD", "qty": 5000 }
    ]
  }
]
```