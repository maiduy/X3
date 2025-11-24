
---

### 1. Cấu hình Thực thể (Core Entities)

#### A. `Characters.json` (Định nghĩa Tướng)
File này định nghĩa danh tính và dữ liệu tĩnh của nhân vật. Lưu ý: Không lưu chỉ số tại mỗi level ở đây, chỉ lưu chỉ số cơ bản (Level 1).

| Tham số (Key) | Kiểu dữ liệu | Giải thích & Logic |
| :--- | :--- | :--- |
| `id` | String (Unique) | Mã định danh duy nhất (VD: `CHAR_FIRE_KNIGHT`). Dùng làm khóa chính cho DB và Analytics. |
| `meta.nameKey` | String | Key trỏ đến file Localization để hiển thị tên đa ngôn ngữ. |
| `meta.role` | Enum | `Tank`, `DPS`, `Support`, `Control`. Dùng để AI chọn vị trí đứng hoặc filter trong UI. |
| `meta.tags` | Array\<String\> | Các thẻ phụ: `Human`, `Dragon`, `Flying` (Dùng cho logic kỹ năng, VD: +10% dmg lên Dragon). |
| `assets.prefab` | String | **Addressable Key** trỏ tới 3D Model/Prefab của tướng. Game chỉ tải khi cần. |
| `assets.icon` | String | Addressable Key trỏ tới ảnh đại diện (Avatar). |
| `stats.base` | Object | Chỉ số tại Level 1 (HP, Atk, Def, Spd). |
| `growth.curveId` | String | ID trỏ sang file `GrowthCurves.json`. VD: `CURVE_WARRIOR_S`. |
| `skills` | Array\<String\> | Danh sách Skill ID mà tướng này sở hữu. |

#### B. `Items.json` (Vật phẩm & Trang bị)
Định nghĩa mọi thứ có thể bỏ vào túi đồ.

| Tham số (Key) | Kiểu dữ liệu | Giải thích & Logic |
| :--- | :--- | :--- |
| `id` | String | Mã vật phẩm (VD: `ITEM_HP_POTION`, `GEAR_SWORD_01`). |
| `type` | Enum | `Equipment`, `Consumable`, `Material`, `Currency`. |
| `stackLimit` | Integer | Số lượng tối đa trong 1 ô túi đồ (VD: 999 cho Material, 1 cho Gear). |
| `equipment.slot` | Enum | `Weapon`, `Helmet`, `Armor`, `Boots`. Chỉ dùng nếu type là Equipment. |
| `equipment.setBonusId`| String | ID trỏ sang `GearSets.json` (VD: `SET_LIFESTEAL`). |
| `equipment.statPool` | Array | Cấu hình RNG (Random Number Generator) cho dòng phụ. Định nghĩa min/max giá trị có thể roll ra. |
| `useEffect` | Object | Hiệu ứng khi sử dụng (cho Consumable). VD: `{type: "Heal", val: 500}`. |

#### C. `GrowthCurves.json` (Công thức Tăng trưởng)
Tách biệt công thức toán học ra khỏi file nhân vật để dễ tái sử dụng.

| Tham số (Key) | Kiểu dữ liệu | Giải thích & Logic |
| :--- | :--- | :--- |
| `id` | String | VD: `CURVE_TANK_S_TIER` (Công thức cho Tanker hạng S). |
| `formula` | String | Công thức toán học dạng chuỗi. VD: `base * (1 + (level - 1) * 0.05)`. Client sẽ parse chuỗi này để tính stats. |
| `breakpoints` | Array\<Object\> | Các điểm gãy (nếu không dùng công thức tuyến tính). VD: Level 40-50 tăng chậm lại. |

---

### 2. Gameplay & Combat Mechanics

#### D. `Skills.json` (Hệ thống Kỹ năng)
Đây là cấu hình phức tạp nhất, sử dụng mô hình **Timeline** để đồng bộ logic với animation.

| Tham số (Key) | Kiểu dữ liệu | Giải thích & Logic |
| :--- | :--- | :--- |
| `id` | String | VD: `SKILL_FIREBALL`. |
| `activation.cd` | Float | Thời gian hồi chiêu (giây hoặc lượt). |
| `activation.cost` | Object | Tài nguyên tiêu hao (VD: `{mana: 50, hp: 10%}`). |
| `targeting.mode` | Enum | `Single`, `AOE_Circle`, `AOE_Line`, `Self`, `Global`. |
| `targeting.filter` | Object | Logic lọc mục tiêu. VD: `{team: "Enemy", sort: "LowestHP", count: 1}`. |
| `timeline` | Array\<Frame\> | **Trái tim của skill.** Danh sách các hành động theo thời gian. |
| `timeline[i].time` | Float | Thời điểm kích hoạt (tính bằng giây từ lúc bắt đầu cast). |
| `timeline[i].action` | Enum | `PlayAnim`, `SpawnVFX`, `ApplyPayload`, `CameraShake`. |
| `timeline[i].payload` | Object | Dữ liệu chi tiết cho action. Xem bảng dưới. |

**Chi tiết `payload` (Hiệu ứng kỹ năng):**

| Payload Key | Giải thích |
| :--- | :--- |
| `type` | Loại hiệu ứng: `Damage`, `Heal`, `Buff`, `Debuff`, `Summon`. |
| `formula` | Công thức tính toán động: `caster.atk * 2.5 + target.maxHp * 0.05`. |
| `element` | Hệ sát thương (Fire, Water...). |
| `chance` | Tỉ lệ kích hoạt (0.0 - 1.0). |

#### E. `BattleRules.json` (Luật Chiến đấu)
Chứa các hằng số toàn cục.

| Tham số (Key) | Kiểu dữ liệu | Giải thích & Logic |
| :--- | :--- | :--- |
| `elementalMatrix` | Matrix | Bảng tương khắc. VD: `{Fire: {Water: 0.5, Wind: 1.5}}`. |
| `maxDefMitigation` | Float | Giới hạn giảm sát thương tối đa của giáp (VD: 0.85 = 85%). |
| `speedConstant` | Float | Hằng số chia tốc độ để tính lượt đi (Action Bar). |
| `critDmgBase` | Float | Sát thương chí mạng mặc định (thường là 1.5). |

---

### 3. Kinh tế & Gacha (Economy)

#### F. `Banners.json` (Gacha Config)
Kiểm soát việc quay tướng, tỷ lệ và bảo hiểm.

| Tham số (Key) | Kiểu dữ liệu | Giải thích & Logic |
| :--- | :--- | :--- |
| `id` | String | ID Banner (VD: `BANNER_NEW_YEAR`). |
| `activeTime` | DateRange | Thời gian bắt đầu và kết thúc (`UTC`). |
| `cost` | Object | Giá quay 1 lần (VD: `{itemId: "GEM", amount: 160}`). |
| `rates` | Object | Tỷ lệ cơ bản. VD: `{SSR: 0.02, SR: 0.18, R: 0.80}`. |
| `pity.soft` | Integer | Số lần quay bắt đầu tăng tỷ lệ (Soft Pity, VD: 50). |
| `pity.hard` | Integer | Số lần quay chắc chắn ra SSR (Hard Pity, VD: 90). |
| `pickup.targetId` | String | ID tướng được tăng tỷ lệ (Rate-up). |
| `pickup.share` | Float | Tỷ lệ chiếm dụng trong nhóm SSR (VD: 0.5 = 50% khi ra SSR là tướng này). |

#### G. `ShopProducts.json` (Cửa hàng)

| Tham số (Key) | Kiểu dữ liệu | Giải thích & Logic |
| :--- | :--- | :--- |
| `sku` | String | Product ID (khớp với Google Play/App Store nếu là IAP). |
| `price` | Object | Giá tiền (Real currency hoặc Game currency). |
| `content` | Array | Những gì nhận được. VD: `[{id: "GEM", val: 500}, {id: "VIP_PTS", val: 50}]`. |
| `limits.type` | Enum | `Daily`, `Weekly`, `Monthly`, `Lifetime`. |
| `limits.max` | Integer | Số lần mua tối đa. |
| `segment` | String | **Segmentation:** Chỉ hiện gói này cho nhóm user nào (VD: `NEW_USER`, `WHALE`). |

---

### 4. LiveOps & Content

#### H. `Quests.json` (Nhiệm vụ)
Dùng cơ chế lắng nghe sự kiện (Event Listener).

| Tham số (Key) | Kiểu dữ liệu | Giải thích & Logic |
| :--- | :--- | :--- |
| `id` | String | VD: `DAILY_KILL_BOSS`. |
| `resetType` | Enum | `Daily`, `Weekly`, `None` (One-time). |
| `listener.event` | String | Tên sự kiện cần lắng nghe từ Event Bus (VD: `OnEnemyKilled`). |
| `listener.filter` | Object | Điều kiện lọc sự kiện. VD: `{tag: "Boss", levelMin: 10}`. |
| `targetCount` | Integer | Số lượng cần đạt được. |
| `rewards` | Array | Phần thưởng khi hoàn thành. |

#### I. `EventSchedule.json` (Lịch trình & Modifiers)

| Tham số (Key) | Kiểu dữ liệu | Giải thích & Logic |
| :--- | :--- | :--- |
| `id` | String | VD: `EVENT_WEEKEND_BONUS`. |
| `cron` | String | Chuỗi Cron biểu thị lịch lặp lại (VD: `0 0 * * 6,0` cho thứ 7, CN). |
| `modifiers` | Array | **Global Override:** Thay đổi thông số game. |
| `modifiers[i].target`| String | Tham số muốn đổi (VD: `DropRate_Gold`, `Stamina_Cost`). |
| `modifiers[i].op` | Enum | `Multiply`, `Add`, `Set`. |
| `modifiers[i].val` | Float | Giá trị thay đổi (VD: `2.0` cho x2 Gold). |

---

### 5. Hệ thống (System)

#### J. `Manifest.json` (Kiểm soát phiên bản)

| Tham số (Key) | Kiểu dữ liệu | Giải thích & Logic |
| :--- | :--- | :--- |
| `minClientVer` | String | Phiên bản Client tối thiểu bắt buộc. Nếu thấp hơn -> Bắt buộc update App. |
| `files` | Map | Danh sách các file config khác. |
| `files[key].hash` | String | MD5 Hash của file để kiểm tra tính toàn vẹn (chống hack/lỗi tải). |
| `files[key].url` | String | Đường dẫn CDN để tải file. |
| `files[key].critical`| Bool | Nếu `true`, game không thể vào nếu tải file này thất bại. |

#### K. `KillSwitch.json` (Ngắt khẩn cấp)

| Tham số (Key) | Kiểu dữ liệu | Giải thích & Logic |
| :--- | :--- | :--- |
| `maintenance` | Bool | `true` = Bật chế độ bảo trì toàn server. |
| `disabledChars` | Array | Danh sách ID tướng bị khóa (do lỗi game breaking). |
| `disabledFeatures`| Array | Danh sách tính năng bị khóa (VD: `Disable_Gacha`, `Disable_PVP`). |
| `whiteListIPs` | Array | Danh sách IP của Dev/QA được phép vào khi bảo trì. |

---

### Tóm tắt ý nghĩa
Việc định nghĩa chi tiết các thông số này giúp:
1.  **Frontend (Unity):** Biết chính xác cần parse dữ liệu kiểu gì (`float` hay `int`, `array` hay `object`).
2.  **Backend/LiveOps:** Biết chính xác cấu trúc JSON để tạo tool Admin hoặc viết file config thủ công.
3.  **Game Design:** Hiểu rõ "quyền hạn" của mình (có thể chỉnh sửa những gì mà không cần gọi Code).