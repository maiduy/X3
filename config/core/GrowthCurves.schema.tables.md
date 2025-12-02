# GrowthCurves Schema Documentation

### Root Level Fields

| Field         | Type             | Description            |
| ------------- | ---------------- | ---------------------- |
| `$schema`     | string (URI)     | Link schema chuẩn JSON |
| `version`     | string (semver)  | Version cấu hình       |
| `lastUpdated` | string (ISO8601) | Thời gian cập nhật     |
| `curves`      | array            | Danh sách các đối tượng đường cong tăng trưởng     |
| `exponentialCurves`      | array            | Danh sách các đối tượng đường cong mũ     |

### Growth Curve
| Field                     | Type   | Description                   | Note                       |
| -------------------------- | ------ | ------------------------ | ----------------------------- |
| `id`                       | string | ID duy nhất cho curve    | Ví dụ: `CURVE_WARRIOR_S_TIER` |
| `meta.name`                | string | Tên hiển thị             | `"S-Tier Warrior Growth"`     |
| `meta.description`         | string | Mô tả chi tiết           | Dùng cho docs/dev             |
| `meta.category`            | string | Loại nhân vật            | Warrior/Mage/Support/...      |
| `meta.tier`                | string | Độ hiếm                  | SSR / SR / R                  |
| `maxLevel`                 | number | Level tối đa             | 60–80                         |
| `stats`                    | object | Công thức tăng từng stat | hp/atk/def/spd                |
| `stats.[stat].formula`     | string | Công thức toán           | `"base * (1 + ...)"`          |
| `stats.[stat].breakpoints` | array  | Các điểm tính sẵn        | Level + multiplier            |

### Exponential Curve

| Field        | Kiểu   | Description                 | Note                              |
| ------------- | ------ | --------------------- | ---------------------------------- |
| `id`          | string | ID duy nhất           | `CURVE_EXP_REQUIRED`               |
| `meta.name`   | string | Tên                   | `"Experience Required"`            |
| `formula`     | string | Công thức exponential | `"floor(100 * pow(1.1, level-1))"` |
| `breakpoints` | array  | Value theo level      | 1, 10, 20, 30...                   |