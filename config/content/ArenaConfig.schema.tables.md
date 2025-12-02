# ArenaConfig.json Schema Documentation

### Root Level Fields

| Field       | Type             | Description                                         |
| ----------- | ---------------- | --------------------------------------------------- |
| `$schema`     | string (URI)     | Link schema chuẩn JSON |
| `version`     | string (semver)  | Version cấu hình       |
| `lastUpdated` | string (ISO8601) | Thời gian cập nhật     |
| arenaMode   | object           | Cấu hình chế độ PvP xếp hạng (Ranked)               |
| casualMode  | object           | Cấu hình chế độ PvP không xếp hạng (Casual)         |

---

### Arena Mode Object

| Field         | Type   | Description                                           |
| ------------- | ------ | ----------------------------------------------------- |
| id            | string | ID duy nhất của chế độ đấu, định dạng: `ARENA_[TYPE]` |
| meta          | object | Metadata dùng để hiển thị & localization              |
| availability  | object | Điều kiện mở khóa chế độ đấu                          |
| matchmaking   | object | Thuật toán ghép trận và các tham số                   |
| seasons       | object | Cấu hình chu kỳ mùa giải cạnh tranh                   |
| rankingSystem | object | Cấu hình hạng, bậc và tiến trình leo hạng             |
| eloSystem     | object | Cấu hình hệ thống tính điểm ELO                       |
| battleRules   | object | Quy tắc chiến đấu trong đấu trường                    |
| rewards       | object | Cấu hình phần thưởng cho mỗi trận đấu                 |
| leaderboard   | object | Cấu hình bảng xếp hạng toàn cầu                       |

---

### Meta Object

| Field          | Type   | Description                              |
| -------------- | ------ | ---------------------------------------- |
| nameKey        | string | Key dùng để hiển thị tên chế độ đấu      |
| descriptionKey | string | Key dùng để hiển thị mô tả chế độ đấu    |
| icon           | string | Tham chiếu icon hiển thị trong giao diện |

---

### Availability Object

| Field           | Type          | Description                                      |
| --------------- | ------------- | ------------------------------------------------ |
| unlockLevel     | number        | Cấp độ tối thiểu của người chơi để mở chế độ đấu |
| unlockCondition | string | null | Điều kiện bổ sung để mở khóa, null nếu không có  |

---

### Matchmaking Object

| Field             | Type   | Description                                                    |
| ----------------- | ------ | -------------------------------------------------------------- |
| algorithm         | string | Thuật toán ghép trận: ELO, PowerBased, Random, TierBased       |
| matchmakingRange  | number | Chênh lệch ELO / sức mạnh tối đa giữa các người chơi được ghép |
| maxSearchTime     | number | Thời gian tối đa trước khi mở rộng phạm vi ghép trận (giây)    |
| botMatchThreshold | number | Thời gian chờ trước khi đưa ra trận bot (giây)                 |

---

### Seasons Object

| Field         | Type   | Description                                   |
| ------------- | ------ | --------------------------------------------- |
| duration      | number | Độ dài mùa giải cạnh tranh (ngày)             |
| resetDay      | number | Ngày trong tháng để reset mùa giải            |
| preseasonDays | number | Số ngày chuẩn bị trước mùa giải               |
| offseasonDays | number | Số ngày trao thưởng sau khi mùa giải kết thúc |

---

### Ranking System Object

| Field       | Type   | Description                                                     |
| ----------- | ------ | --------------------------------------------------------------- |
| initialRank | string | Hạng bắt đầu của người chơi mới, định dạng: `{Tier}_{Division}` |
| tiers       | array  | Danh sách các bậc hạng theo thứ tự tăng dần                     |

---

### Tier Object

| Field              | Type             | Description                                                                 |
| ------------------ | ---------------- | --------------------------------------------------------------------------- |
| tier               | string           | Tên bậc hạng (Bronze, Silver, Gold, Platinum, Diamond, Master, Grandmaster) |
| divisions          | number           | Số phân bậc trong bậc hạng này                                              |
| eloRange           | object           | Khoảng ELO tối thiểu và tối đa của bậc hạng                                 |
| divisionThresholds | array            | Điểm ELO tối thiểu cho từng phân bậc                                        |
| icon               | string           | Tham chiếu icon huy hiệu hạng                                               |
| limitedSlots       | number | omitted | Số lượng người chơi tối đa, tuỳ chọn cho các bậc hạng cao                   |
| rewards            | object           | Phần thưởng hàng tuần và theo mùa                                           |

---

### ELO System Object

| Field              | Type   | Description                              |
| ------------------ | ------ | ---------------------------------------- |
| kFactor            | number | Hệ số K của ELO để tính thay đổi điểm    |
| placementMatches   | number | Số trận đấu ban đầu để định hạng         |
| maxGainPerMatch    | number | Điểm ELO tối đa nhận được mỗi trận thắng |
| maxLossPerMatch    | number | Điểm ELO tối đa bị trừ mỗi trận thua     |
| winStreakBonus     | object | Cấu hình thưởng chuỗi thắng liên tiếp    |
| demotionProtection | object | Cấu hình bảo vệ không bị tụt hạng        |

---

### Battle Rules Object

| Field     | Type   | Description                         |
| --------- | ------ | ----------------------------------- |
| teamSize  | number | Số lượng nhân vật mỗi đội           |
| banPhase  | object | Cấu hình hệ thống cấm chọn nhân vật |
| pickPhase | object | Cấu hình hệ thống chọn đội          |
| turnLimit | number | Số lượt tối đa trước khi hòa        |
| timeLimit | number | Thời gian tối đa trận đấu (giây)    |

---

### Rewards Object

| Field         | Type  | Description                                          |
| ------------- | ----- | ---------------------------------------------------- |
| perWin        | array | Phần thưởng cho trận thắng                           |
| perLoss       | array | Phần thưởng an ủi cho trận thua                      |
| firstWinOfDay | array | Phần thưởng thêm cho chiến thắng đầu tiên trong ngày |

---

### Leaderboard Object

| Field           | Type   | Description                                    |
| --------------- | ------ | ---------------------------------------------- |
| updateFrequency | number | Tần suất cập nhật bảng xếp hạng (giây)         |
| displayTopN     | number | Số lượng người chơi hàng đầu hiển thị          |
| topRewards      | array  | Phần thưởng đặc biệt cho các vị trí/range hạng |

---

### Casual Mode Object

| Field       | Type          | Description                                    |
| ----------- | ------------- | ---------------------------------------------- |
| noEloChange | boolean       | Chế độ trận đấu không ảnh hưởng điểm ELO       |
| dailyLimit  | number | null | Số trận tối đa mỗi ngày, null = không giới hạn |
| matchmaking | object        | Cấu hình ghép trận cho chế độ Casual           |
| battleRules | object        | Điều chỉnh luật đấu cho chế độ Casual          |
| rewards     | object        | Phần thưởng mỗi trận trong chế độ Casual       |
