-----

# Modular Config-Driven Game Architecture

**Technical Design Document**

## 1\. Tổng quan & Tầm nhìn (Overview & Vision)

[cite\_start]Tài liệu này mô tả kiến trúc kỹ thuật cho các dòng game Team Battle/RPG, chuyển đổi từ mô hình phát triển truyền thống sang mô hình **Modular & Config-Driven**[cite: 1, 10].

### Mục tiêu cốt lõi

  * [cite\_start]**Decoupling (Gỡ bỏ phụ thuộc):** Tách biệt hoàn toàn Logic (Code) và Dữ liệu (Config/Assets)[cite: 11, 42].
  * [cite\_start]**Scalability (Khả năng mở rộng):** Dễ dàng thêm chế độ chơi, tướng mới, hoặc sự kiện mà không cần build lại Client[cite: 4, 50].
  * [cite\_start]**Reusability (Tái sử dụng):** Xây dựng "Engine" dùng chung cho các dự án RPG/Strategy trong tương lai[cite: 2, 55].

### Nguyên lý vận hành

> "Code là Cỗ máy (Engine), Config là Nhiên liệu (Fuel)."

  * [cite\_start]**Game Designer:** Kiểm soát cân bằng game, luồng chơi, và sự kiện thông qua JSON/Excel[cite: 38].
  * [cite\_start]**Engineer:** Tập trung xây dựng các hệ thống xử lý dữ liệu (Skill Engine, Quest System) bền vững[cite: 8].

-----

## 2\. Bảng Tổng Hợp Hệ Thống Config (Master Config Table)

Bảng ánh xạ các module chức năng sang các file cấu hình tương ứng.

| Phân Hệ (System) | Module Con | File Config (JSON) | Tham số Chính & Chức năng |
| :--- | :--- | :--- | :--- |
| **CORE ENTITY** | Character | `Characters.json` | `BaseStats`, `AssetKey` (Addressables), `Rarity`. [cite\_start]Định nghĩa tướng tĩnh[cite: 64]. |
| | Gear/Item | `Items.json` | `StatRollRange` (Min-Max), `SetBonuses`. [cite\_start]Định nghĩa trang bị & chỉ số ngẫu nhiên[cite: 75]. |
| **GAMEPLAY** | Skill Engine | `Skills.json` | `Triggers`, `TargetLogic`, `Formulas`, `VFXKeys`. [cite\_start]Logic chiêu thức phức tạp[cite: 69]. |
| | Combat Rules | `BattleRules.json` | `TurnPacing`, `ElementalMatrix` (Khắc hệ), `ManaRules`. [cite\_start]Luật chơi nền tảng[cite: 66]. |
| | AI Behavior | `AIProfiles.json` | `DecisionTree`, `PriorityWeights`. [cite\_start]Cấu hình trí tuệ nhân tạo cho Auto/Enemy[cite: 30]. |
| **CONTENT** | PvE Campaign | `Levels.json` | `EnemyWaves`, `MapID`, `WinConditions`. Cấu hình ải và quái vật. |
| | PvP Arena | `ArenaConfig.json` | `EloBuckets`, `SeasonDates`, `RankRewards`. Luật ghép trận và xếp hạng. |
| **ECONOMY** | Gacha | `Banners.json` | `RateTable` (SSR/SR), `PityCount`. [cite\_start]Tỷ lệ quay tướng và bảo hiểm[cite: 86]. |
| | Shop/IAP | `ShopProducts.json` | `Price`, `Contents`, `Limits`. Gói nạp và vật phẩm cửa hàng. |
| **LIVEOPS** | Events | `EventSchedule.json` | `ActiveTime`, `GlobalModifiers`. Kích hoạt sự kiện từ xa. |
| | Quests | `Quests.json` | `TriggerEvent`, `TargetCount`, `Rewards`. Nhiệm vụ động. |

-----

## 3\. Chi tiết Schema Cấu hình (Detailed Schemas)

### 3.1. Universal Skill Config (Hệ thống Kỹ năng)

[cite\_start]Mô hình hóa mọi hành động trong chiến đấu dưới dạng dữ liệu để "Skill Execution Engine" xử lý[cite: 27].

```json
{
  "id": "SKILL_PHOENIX_STRIKE",
  "meta": {
    "nameKey": "SKILL_NAME_PHOENIX",
    "icon": "Icon_Fire_01",
    "tags": ["Fire", "Melee", "Burst"]
  },
  "activation": {
    "cooldown": 12.0,
    "cost": [{ "resource": "Mana", "value": 50 }]
  },
  "targeting": {
    "mode": "TargetUnit",
    "range": 4.0,
    "filter": { "team": "Enemy", "sort": "LowestHP" }
  },
  "timeline": [
    { 
      "time": 0.0, 
      "action": "PlayAnim", 
      "param": "Attack_Ulti" 
    },
    { 
      "time": 0.5, 
      "action": "ApplyEffects", 
      "payload": [
        { 
          "type": "Damage", 
          "formula": "(caster.atk * 3.5) + (target.maxHp * 0.05)", 
          "element": "Fire" 
        },
        { 
          "type": "ApplyStatus", 
          "id": "Burn_Dot", 
          "duration": 3, 
          "chance": 0.8 
        }
      ] 
    }
  ]
}
```

### 3.2. Gacha & Economy Config (Hệ thống Kinh tế)

[cite\_start]Kiểm soát tỷ lệ và doanh thu hoàn toàn từ Server (Server-authoritative)[cite: 86].

```json
{
  "bannerId": "BANNER_FIRE_DRAGON_V1",
  "duration": { "start": "2025-11-01", "end": "2025-11-15" },
  "cost": { "itemId": "SUMMON_SCROLL", "amount": 1 },
  "pity": {
    "soft": 50, // Tăng tỷ lệ sau 50 lần
    "hard": 90, // Chắc chắn ra SSR lần 90
    "resetOnSSR": true
  },
  "rates": {
    "SSR": 0.02,
    "SR": 0.18,
    "R": 0.80
  },
  "pickup": { 
    "heroId": "HERO_FIRE_DRAGON", 
    "rateShare": 0.5 // Chiếm 50% tỷ lệ ra SSR
  }
}
```

### 3.3. Quest & Mission System (Hệ thống Nhiệm vụ)

Sử dụng cơ chế **Event Listener** để tự động theo dõi tiến độ mà không cần hardcode logic kiểm tra.

```json
{
  "questId": "DAILY_SLAYER_01",
  "type": "Daily",
  "listeners": [
    {
      "event": "OnEnemyKilled", // Lắng nghe sự kiện quái chết
      "filters": { 
        "tag": "Goblin", 
        "level_min": 5 
      },
      "requiredCount": 10
    }
  ],
  "rewards": [
    { "itemId": "GOLD", "amount": 1000 }
  ]
}
```

-----

## 4\. Chiến lược Triển khai trên Unity (Implementation Strategy)

Để đảm bảo hiệu năng (Performance) và quy trình làm việc (Workflow) tối ưu:

### 4.1. Hybrid Data Loading (Tiếp cận Lai ghép)

Kết hợp sức mạnh của Unity ScriptableObject và tính linh hoạt của JSON.

1.  **Static Data (ScriptableObject):** Dùng cho ID, Texture, Model refs. Load nhanh, quản lý bộ nhớ tốt.
2.  **Dynamic Data (JSON):** Dùng cho Stats, Formulas, Rates. Đè (Override) lên dữ liệu tĩnh khi tải về từ Server.
      * *Luồng:* `BaseCharacterSO` (Client) \<--- `BalancePatch.json` (Server).

### 4.2. Asset Management (Quản lý Tài nguyên)

Sử dụng **Unity Addressables**:

  * Trong Config JSON chỉ lưu `AssetKey` (String), không lưu Direct Reference.
  * Game chỉ tải Model/VFX khi thực sự cần (Lazy Loading) để giảm RAM usage.

### 4.3. Data Pipeline (Quy trình Dữ liệu)

1.  **Authoring:** Designer nhập liệu trên Google Sheets/Excel hoặc Custom Tool.
2.  **Build:** Tool CI/CD validate dữ liệu -\> Export ra JSON.
3.  **Deploy:** Upload JSON lên CDN (AWS S3).
4.  **Runtime:** Client tải JSON mới nhất khi khởi động (Bootstrap) -\> Parse vào Memory (Dictionary).

-----

## 5\. Lộ trình Cải tiến (Roadmap)

[cite\_start]Dựa trên kế hoạch trong tài liệu gốc [cite: 56-59]:

1.  **Phase 1 (Foundation):** Xây dựng Config Service, Input Manager và Event Bus.
2.  **Phase 2 (Core):** Hoàn thiện Skill Engine đọc JSON, hệ thống Combat Rules.
3.  **Phase 3 (Meta):** Xây dựng hệ thống Inventory, Quest và Gacha định hướng config.
4.  **Phase 4 (LiveOps):** Công cụ CMS để Designer tự push config hot-update.