# GearSets Schema Documentation

### Root Level Fields

| Field         | Type             | Description                         |
| ------------- | ---------------- | ----------------------------------- |
| `$schema`     | string (URI)     | Link schema chuẩn JSON              |
| `version`     | string (semver)  | Version cấu hình                    |
| `lastUpdated` | string (ISO8601) | Thời gian cập nhật                  |
| `sets`        | array            | Danh sách các bộ trang bị           |
| `buffs`       | array            | Danh sách Buff (hiệu ứng có lợi)    |
| `debuffs`     | array            | Danh sách Debuff (hiệu ứng bất lợi) |

### Gear Set Object

| Field           | Type   | Description                                |
| --------------- | ------ | ------------------------------------------ |
| `id`            | string | ID duy nhất của bộ, định dạng `SET_[NAME]` |
| `meta`          | object | Metadata hiển thị & phân loại              |
| `pieces`        | object | Các bonus khi mặc nhiều món cùng bộ        |
| `requiredSlots` | array  | Danh sách ô trang bị tính vào bộ           |

### Meta Object

| Field            | Type   | Description                                    |
| ---------------- | ------ | ---------------------------------------------- |
| `nameKey`        | string | Key tên bộ hiển thị                            |
| `descriptionKey` | string | Key mô tả hoặc lore của bộ                     |
| `icon`           | string | Biểu tượng đại diện cho bộ                     |
| `element`        | enum   | Fire, Ice, Light, Dark, Nature, Wind, Physical |
| `tier`           | enum   | R, SR, SSR                                     |

### Pieces Object

| Field | Type   | Description                              |
| ----- | ------ | ---------------------------------------- |
| `"2"` | object | Bonus khi mặc 2 món                      |
| `"4"` | object | Bonus khi mặc 4 món (cộng dồn với 2 món) |
| `"6"` | object | Bonus tùy chọn cho bộ có 6+ món          |

### Bonus Object (2/4/6 Piece)

| Field         | Type   | Description                           |
| ------------- | ------ | ------------------------------------- |
| `name`        | string | Tên của bậc bonus                     |
| `description` | string | Mô tả hiệu ứng rõ ràng cho người chơi |
| `effects`     | array  | Danh sách các Effect Object           |

### Effect Object Types

| Type                | Description                                |
| ------------------- | ------------------------------------------ |
| `StatBonus`         | Tăng vĩnh viễn một chỉ số nhân vật         |
| `ElementalDmgBonus` | Tăng sát thương của một hoặc nhiều phần tử |
| `OnSkillUse`        | Kích hoạt khi sử dụng kỹ năng              |
| `OnHit`             | Kích hoạt khi nhân vật bị tấn công         |
| `Lifesteal`         | Hồi máu dựa trên % sát thương gây ra       |
| `ConditionalBuff`   | Buff chỉ áp dụng khi điều kiện đúng        |
| `OnBattleStart`     | Kích hoạt khi trận đấu bắt đầu             |

### Buff Object

| Field     | Type   | Description                                   |
| --------- | ------ | --------------------------------------------- |
| `id`      | string | ID duy nhất của buff, định dạng `BUFF_[NAME]` |
| `name`    | string | Tên hiển thị cho người chơi                   |
| `type`    | string | `"Buff"`                                      |
| `icon`    | string | Biểu tượng hiển thị trong UI                  |
| `effects` | array  | Các hiệu ứng cơ học của buff                  |
| `visual`  | object | Hiệu ứng hiển thị (VFX, màu sắc)              |

### Debuff Object

| Field     | Type   | Description                                       |
| --------- | ------ | ------------------------------------------------- |
| `id`      | string | ID duy nhất của debuff, định dạng `DEBUFF_[NAME]` |
| `name`    | string | Tên hiển thị cho người chơi                       |
| `type`    | string | `"Debuff"`                                        |
| `icon`    | string | Biểu tượng hiển thị trong UI                      |
| `effects` | array  | Các hiệu ứng gây bất lợi                          |
| `visual`  | object | Hiệu ứng hiển thị (VFX, màu sắc)                  |
