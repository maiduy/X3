# Levels Schema Documentation

### Root Level Fields

| Field         | Type             | Description                                     |
| ------------- | ---------------- | ----------------------------------------------- |
| `$schema`     | string (URI)     | Link schema chuẩn JSON                          |
| `version`     | string (semver)  | Version cấu hình                                |
| `lastUpdated` | string (ISO8601) | Thời gian cập nhật                              |
| `campaigns`   | array            | Danh sách campaign câu chuyện chính             |
| `dungeons`    | array            | Danh sách dungeon farm tài nguyên hoặc vật liệu |

---

### Campaign Object

| Field      | Type   | Description                            |
| ---------- | ------ | -------------------------------------- |
| `id`       | string | ID duy nhất của campaign               |
| `meta`     | object | Metadata hiển thị & thông tin campaign |
| `chapters` | array  | Danh sách các chapter trong campaign   |

#### Campaign Meta Object

| Field            | Type   | Description                      |
| ---------------- | ------ | -------------------------------- |
| `nameKey`        | string | Key hiển thị tên campaign        |
| `descriptionKey` | string | Key hiển thị mô tả campaign      |
| `icon`           | string | Icon đại diện campaign           |
| `chapters`       | number | Số chapter trong campaign        |
| `unlockLevel`    | number | Level người chơi tối thiểu để mở |

---

### Chapter Object

| Field    | Type   | Description                           |
| -------- | ------ | ------------------------------------- |
| `id`     | string | ID duy nhất của chapter               |
| `meta`   | object | Metadata hiển thị & thông tin chapter |
| `stages` | array  | Danh sách stage trong chapter         |

#### Chapter Meta Object

| Field           | Type   | Description                        |
| --------------- | ------ | ---------------------------------- |
| `nameKey`       | string | Key hiển thị tên chapter           |
| `chapterNumber` | number | Số thứ tự chapter                  |
| `storyUnlock`   | string | ID cắt cảnh hoặc hội thoại mở khóa |

---

### Stage Object

| Field              | Type   | Description                             |
| ------------------ | ------ | --------------------------------------- |
| `id`               | string | ID duy nhất của stage                   |
| `meta`             | object | Metadata hiển thị & thông tin stage     |
| `requirements`     | object | Điều kiện mở khóa & tiêu hao năng lượng |
| `mapConfig`        | object | Cấu hình bản đồ & môi trường            |
| `enemies`          | object | Cấu hình kẻ thù & làn sóng              |
| `victoryCondition` | object | Điều kiện chiến thắng stage             |
| `rewards`          | object | Phần thưởng khi hoàn thành stage        |
| `drops`            | object | Loot ngẫu nhiên                         |
| `stars`            | array  | Mục tiêu để nhận sao                    |

#### Stage Meta Object

| Field              | Type    | Description                          |
| ------------------ | ------- | ------------------------------------ |
| `nameKey`          | string  | Key hiển thị tên stage               |
| `stageNumber`      | number  | Số thứ tự stage trong chapter        |
| `difficulty`       | string  | Mức độ khó: Normal, Hard, Hell, Boss |
| `recommendedPower` | number  | Lực chiến đề xuất                    |
| `isBossStage`      | boolean | Có phải boss stage hay không         |

---

### Requirements Object

| Field             | Type        | Description                 |
| ----------------- | ----------- | --------------------------- |
| `unlockCondition` | string/null | Stage prerequisite          |
| `minLevel`        | number      | Level tối thiểu để tham gia |
| `energyCost`      | number      | Năng lượng tiêu hao         |

---

### Map Config Object

| Field           | Type        | Description         |
| --------------- | ----------- | ------------------- |
| `mapId`         | string      | ID bản đồ chiến đấu |
| `environment`   | string      | Môi trường/biome    |
| `weatherEffect` | string/null | Hiệu ứng thời tiết  |

---

### Enemies Object

| Field   | Type  | Description               |
| ------- | ----- | ------------------------- |
| `waves` | array | Danh sách làn sóng kẻ thù |

#### Wave Object

| Field        | Type   | Description                     |
| ------------ | ------ | ------------------------------- |
| `waveNumber` | number | Số thứ tự làn sóng              |
| `units`      | array  | Danh sách kẻ thù trong làn sóng |

#### Enemy Unit Object

| Field       | Type    | Description            |
| ----------- | ------- | ---------------------- |
| `enemyId`   | string  | ID kẻ thù              |
| `level`     | number  | Level kẻ thù           |
| `position`  | number  | Vị trí chiến đấu (0-4) |
| `isBoss`    | boolean | Kẻ thù là boss không   |
| `aiProfile` | string  | AI pattern (tuỳ chọn)  |

---

### Victory Condition Object

| Field    | Type        | Description                                                               |
| -------- | ----------- | ------------------------------------------------------------------------- |
| `type`   | string      | Loại chiến thắng: ELIMINATE_ALL, KILL_BOSS, SURVIVE_TURNS, PROTECT_TARGET |
| `params` | object/null | Tham số bổ sung cho điều kiện thắng                                       |

---

### Rewards Object

| Field        | Type   | Description                    |
| ------------ | ------ | ------------------------------ |
| `firstClear` | array  | Phần thưởng lần đầu hoàn thành |
| `repeat`     | array  | Phần thưởng lặp lại            |
| `exp`        | number | EXP nhân vật                   |
| `playerExp`  | number | EXP người chơi                 |

---

### Dungeon Object

| Field          | Type   | Description                           |
| -------------- | ------ | ------------------------------------- |
| `id`           | string | ID dungeon                            |
| `meta`         | object | Metadata hiển thị & thông tin dungeon |
| `difficulties` | array  | Các cấp độ khó                        |
| `dailyLimit`   | number | Số lần chơi tối đa mỗi ngày           |

#### Dungeon Meta Object

| Field            | Type   | Description                                            |
| ---------------- | ------ | ------------------------------------------------------ |
| `nameKey`        | string | Key hiển thị tên dungeon                               |
| `descriptionKey` | string | Key mô tả dungeon                                      |
| `icon`           | string | Icon dungeon                                           |
| `type`           | string | Loại dungeon: Resource, Material, Equipment, Challenge |
| `availability`   | object | Lịch mở dungeon (daysOfWeek, unlockLevel)              |
