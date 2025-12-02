# Characters Schema Documentation

### Root Level Fields

| Field         | Type             | Description            |
| ------------- | ---------------- | ---------------------- |
| `$schema`     | string (URI)     | Link schema chuẩn JSON |
| `version`     | string (semver)  | Version cấu hình       |
| `lastUpdated` | string (ISO8601) | Thời gian cập nhật     |
| `characters`  | array            | Danh sách nhân vật     |

### Character Object
| Field                | Type   | Description                   |
| -------------------- | ------ | ----------------------------- |
| `id`                 | string | ID duy nhất của nhân vật      |
| `meta`               | object | Metadata hiển thị & phân loại |
| `baseStats`          | object | Chỉ số khởi điểm              |
| `growthCurveId`      | string | ID đường cong tăng trưởng     |
| `assets`             | object | Asset tham chiếu              |
| `skillSet`           | object | Bộ kỹ năng                    |
| `unlockRequirements` | object | Điều kiện mở khóa             |
| `ascension`          | object | Nâng cấp giới hạn (ascend)    |

### Meta Object
| Field            | Type   | Description           |
| ---------------- | ------ | --------------------- |
| `nameKey`        | string | Key tên nhân vật      |
| `descriptionKey` | string | Key mô tả             |
| `icon`           | string | Icon portrait         |
| `model`          | string | Model 3D/sprite       |
| `rarity`         | enum   | R, SR, SSR            |
| `element`        | enum   | Fire, Ice, Light...   |
| `roles`          | array  | DPS, Tank, Support... |
| `factions`       | array  | Nhóm/đội/lore         |
| `gender`         | enum   | Male / Female / Other |
| `tags`           | array  | Tag gameplay          |

### Base Stats
| Field       | Type  | Description         |
| ----------- | ----- | ------------------- |
| `level`     | int   | Mặc định 1          |
| `maxLevel`  | int   | Level tối đa        |
| `hp`        | int   | Máu                 |
| `atk`       | int   | Tấn công            |
| `def`       | int   | Phòng thủ           |
| `spd`       | int   | Tốc độ              |
| `crit`      | float | Tỷ lệ chí mạng      |
| `critDmg`   | float | Sát thương chí mạng |
| `accuracy`  | float | Chính xác           |
| `evasion`   | float | Né tránh            |
| `effectRes` | float | Kháng hiệu ứng      |


### Assets
| Field        | Type   | Description         |
| ------------ | ------ | ------------------- |
| `prefab`     | string | Đường dẫn prefab    |
| `animations` | string | Bộ animation        |
| `portrait`   | string | Ảnh portrait        |
| `skillVFX`   | string | Bộ hiệu ứng kỹ năng |

### Skill Set
| Field      | Type   | Description       |
| ---------- | ------ | ----------------- |
| `basic`    | array | Skill đánh thường |
| `skill1`   | array | Skill chủ động 1  |
| `skill2`   | array | Skill chủ động 2  |
| `ultimate` | array | Ulti              |
| `passive`  | array | Kỹ năng bị động   |

### Unlock Requirements
| Field             | Type    | Description                             |
| ----------------- | ------- | --------------------------------------- |
| `defaultUnlocked` | boolean | Có sẵn từ đầu                           |
| `unlockMethod`    | enum    | Tutorial / Story / Gacha / Event / Shop |
| `fragments`       | int     | Số mảnh mở khóa                         |

### Ascension
| Field          | Type  | Description          |
| -------------- | ----- | -------------------- |
| `maxAscension` | int   | Số tầng ascend       |
| `materials`    | array | Nguyên liệu nâng cấp |

### Material Object
| Field    | Type   | Description    |
| -------- | ------ | -------------- |
| `itemId` | string | ID nguyên liệu |
| `count`  | int    | Số lượng cần   |
