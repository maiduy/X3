# Items Schema Documentation

## Root Level Fields

| Field         | Type             | Description                                          |
| ------------- | ---------------- | ---------------------------------------------------- |
| `$schema`     | string (URI)     | Link schema chuẩn JSON |
| `version`     | string (semver)  | Version cấu hình       |
| `lastUpdated` | string (ISO8601) | Thời gian cập nhật     |
| `items`       | array            | Danh sách đầy đủ tất cả các vật phẩm trong game      |

---

## Item Object

| Field           | Type           | Description                                            |
| --------------- | -------------- | ------------------------------------------------------ |
| `id`            | string         | ID duy nhất và không thể thay đổi của vật phẩm         |
| `meta`          | object         | Metadata để hiển thị và phân loại                      |
| `baseStats`     | object         | Chỉ số khởi điểm tại cấp 1 (chỉ dành cho trang bị)     |
| `statRollRange` | object         | Khoảng ngẫu nhiên của các chỉ số trang bị              |
| `subStats`      | object         | Các chỉ số phụ và số lượng tối đa có thể có            |
| `setBonus`      | object or null | Thông tin bonus khi mặc set trang bị                   |
| `uniqueEffect`  | object or null | Hiệu ứng đặc biệt riêng (chỉ cho trang bị SSR)         |
| `enhancement`   | object         | Hệ thống nâng cấp trang bị                             |
| `effect`        | object         | Hiệu ứng khi dùng vật phẩm (chỉ cho consumables)       |
| `usageRules`    | object         | Quy tắc sử dụng và quản lý vật phẩm                    |
| `sources`       | array          | Nguồn lấy vật phẩm (chỉ cho nguyên liệu)               |
| `iapMapping`    | object         | Thông tin mua vật phẩm bằng tiền thật (premium)        |
| `gachaMapping`  | object         | Cấu hình vé gacha / summon (chỉ cho vật phẩm đặc biệt) |
| `assets`        | object         | Tham chiếu tài nguyên hình ảnh và âm thanh             |

---

## Meta Object

| Field            | Type           | Description                                                                                               |
| ---------------- | -------------- | --------------------------------------------------------------------------------------------------------- |
| `nameKey`        | string         | Key dùng để hiển thị tên vật phẩm                                                                         |
| `descriptionKey` | string         | Key dùng để hiển thị mô tả / lore vật phẩm                                                                |
| `icon`           | string         | ID icon hiển thị trong inventory                                                                          |
| `model`          | string or null | Mô hình 3D hoặc sprite (null nếu vật phẩm không có hình)                                                  |
| `type`           | string         | Loại chính của vật phẩm (`Weapon`, `Armor`, `Accessory`, `Consumable`, `Material`, `Currency`, `Special`) |
| `subType`        | string         | Loại con chi tiết hơn trong từng type                                                                     |
| `rarity`         | string         | Độ hiếm của vật phẩm (`Common`, `R`, `SR`, `SSR`, `Premium`)                                              |
| `maxLevel`       | number         | Mức nâng cấp tối đa cho vật phẩm                                                                          |
| `slot`           | string or null | Vị trí trang bị trên nhân vật (null nếu không phải trang bị)                                              |

---

## Base Stats Object (Trang bị)

| Field       | Type   | Description                                       |
| ----------- | ------ | ------------------------------------------------- |
| `atk`       | number | Lượng tấn công thêm                               |
| `def`       | number | Lượng phòng thủ thêm                              |
| `hp`        | number | Lượng HP thêm                                     |
| `critRate`  | number | Tỉ lệ chí mạng (dạng thập phân, ví dụ 0.12 = 12%) |
| `critDmg`   | number | Hệ số sát thương chí mạng                         |
| `effectRes` | number | Kháng hiệu ứng tiêu cực (dạng thập phân)          |

---

## Stat Roll Range

| Field | Type   | Description                       |
| ----- | ------ | --------------------------------- |
| min   | number | Giá trị tối thiểu khi roll chỉ số |
| max   | number | Giá trị tối đa khi roll chỉ số    |

---

## Sub Stats Object

| Field         | Type   | Description                               |
| ------------- | ------ | ----------------------------------------- |
| `maxSubStats` | number | Số lượng tối đa các chỉ số phụ (0-4)      |
| `pool`        | array  | Danh sách chỉ số phụ và trọng số xác suất |

### Sub-Stat Pool Entry

| Field        | Type   | Description                                       |
| ------------ | ------ | ------------------------------------------------- |
| `stat`       | string | Tên chỉ số phụ (`hp`, `atk%`, `def%`, `spd`, ...) |
| `weight`     | number | Trọng số xác suất khi roll                        |
| `valueRange` | object | Khoảng giá trị min/max cho chỉ số phụ             |

---

## Set Bonus Object

| Field     | Type   | Description                   |
| --------- | ------ | ----------------------------- |
| `setId`   | string | ID của set trang bị           |
| `2pieces` | object | Bonus khi mặc 2 món thuộc set |
| `4pieces` | object | Bonus khi mặc 4 món thuộc set |

### Set Bonus Fields

| Field         | Type   | Description                         |
| ------------- | ------ | ----------------------------------- |
| `description` | string | Mô tả bonus hiển thị cho người chơi |
| `effects`     | array  | Hiệu ứng cơ chế (Effect Objects)    |

---

## Unique Effect Object (Accessory SSR)

| Field         | Type   | Description                   |
| ------------- | ------ | ----------------------------- |
| `name`        | string | Tên hiệu ứng                  |
| `description` | string | Mô tả hiệu ứng cho người chơi |
| `effects`     | array  | Hiệu ứng cơ chế thực tế       |

---

## Enhancement Object

| Field            | Type   | Description                         |
| ---------------- | ------ | ----------------------------------- |
| `maxEnhancement` | number | Mức nâng cấp tối đa                 |
| `costPerLevel`   | array  | Danh sách chi phí theo cấp nâng cấp |

### Cost Tier Fields

| Field       | Type   | Description                     |
| ----------- | ------ | ------------------------------- |
| `level`     | number | Cấp nâng cấp                    |
| `gold`      | number | Chi phí vàng                    |
| `materials` | array  | Danh sách nguyên liệu cần thiết |

### Material Requirement

| Field    | Type   | Description                            |
| -------- | ------ | -------------------------------------- |
| `itemId` | string | ID nguyên liệu (tham chiếu Items.json) |
| `count`  | number | Số lượng cần dùng                      |

---

## Effect Object (Consumable)

| Field     | Type   | Description                                       |
| --------- | ------ | ------------------------------------------------- |
| `type`    | string | Loại hiệu ứng (`InstantHeal`, `InstantMana`, ...) |
| `value`   | number | Giá trị cơ bản                                    |
| `formula` | string | Công thức tính toán giá trị hiệu ứng              |

---

## Usage Rules Object

| Field            | Type    | Description                               |
| ---------------- | ------- | ----------------------------------------- |
| `usableInCombat` | boolean | Có thể dùng trong trận đấu                |
| `cooldown`       | number  | Thời gian chờ giữa các lần sử dụng (giây) |
| `maxStack`       | number  | Số lượng tối đa trong 1 stack             |
| `consumeOnUse`   | boolean | Vật phẩm bị tiêu khi sử dụng              |
| `tradeable`      | boolean | Có thể giao dịch giữa người chơi          |
| `sellable`       | boolean | Có thể bán cho NPC                        |
| `sellPrice`      | number  | Số vàng nhận được khi bán                 |

---

## Sources Object (Material)

| Field      | Type   | Description                               |
| ---------- | ------ | ----------------------------------------- |
| `type`     | string | Loại nguồn (`Drop`, `Shop`, `Craft`, ...) |
| `sourceId` | string | ID nguồn cụ thể                           |
| `dropRate` | number | Xác suất rơi (dạng thập phân)             |
| `cost`     | object | Chi phí nếu mua (itemId + amount)         |

---

## IAP Mapping Object (Premium Currency)

| Field          | Type    | Description                                  |
| -------------- | ------- | -------------------------------------------- |
| `canPurchase`  | boolean | Có thể mua bằng tiền thật                    |
| `exchangeRate` | number  | Tỉ lệ chuyển đổi từ IAP sang tiền trong game |

---

## Gacha Mapping Object (Special Items)

| Field          | Type   | Description                        |
| -------------- | ------ | ---------------------------------- |
| `validBanners` | array  | Danh sách banner có thể sử dụng vé |
| `pullCount`    | number | Số lần kéo khi dùng vé             |

---

## Assets Object

| Field    | Type   | Description                                   |
| -------- | ------ | --------------------------------------------- |
| `prefab` | string | Prefab 3D cho trang bị hiển thị trên nhân vật |
| `icon`   | string | Icon hiển thị trong inventory                 |
| `vfx`    | string | Hiệu ứng hình ảnh khi sử dụng (tùy chọn)      |
