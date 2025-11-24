
---

# 🧩 **1. Thông tin cấu trúc tổng quan**

JSON này mô tả **Master Character Config** trong một game RPG/Battle Game, gồm nhiều nhân vật và các thuộc tính:

* `meta` – Thông tin mô tả nhân vật
* `baseStats` – Chỉ số gốc của nhân vật khi Level 1
* `growthCurves` – Công thức tăng chỉ số theo cấp
* `assets` – Dữ liệu liên kết tới model, ảnh, hiệu ứng
* `skillSet` – Bộ kỹ năng
* `unlockRequirements` – Điều kiện mở khóa
* `ascension` – Nguyên liệu nâng cấp bậc (Ascend)

Bây giờ ta giải thích **từng trường** một cách chi tiết.

---

# 🏷️ **2. meta – Thông tin định danh nhân vật**

| Thuộc tính         | Ý nghĩa                                                            |
| ------------------ | ------------------------------------------------------------------ |
| **id**             | Mã định danh duy nhất của nhân vật trong hệ thống.                 |
| **nameKey**        | Key để map tới file đa ngôn ngữ (English/Chinese/Korean…).         |
| **descriptionKey** | Key mô tả nhân vật trong file localization.                        |
| **icon**           | Icon Portrait hiển thị trong UI.                                   |
| **model**          | ID prefab/model 3D của nhân vật trong Unity.                       |
| **rarity**         | Độ hiếm (R, SR, SSR…). Ảnh hưởng đến sức mạnh, tỉ lệ gacha.        |
| **element**        | Nguyên tố: Fire, Ice, Light, Dark, Nature… (ảnh hưởng tương khắc). |
| **role**           | Vai trò: DPS, Tank, Support…                                       |
| **faction**        | Phe/Faction để dùng vào synergy đội hình.                          |
| **tags**           | Các thẻ mô tả phong cách chiến đấu: Melee, Burst, AoE, Control…    |

**Ý nghĩa:** nhóm này dùng cho UI/UX, hiển thị, phân loại, và gameplay meta.

---

# 🧪 **3. baseStats – Chỉ số gốc nhân vật**

Đây là **stat khi Level 1, Ascension 0**.

| Thuộc tính    | Giải thích                                                        |
| ------------- | ----------------------------------------------------------------- |
| **level**     | Level khởi tạo.                                                   |
| **maxLevel**  | Level tối đa (trước khi tăng Ascension).                          |
| **hp**        | Máu. Khi = 0 thì nhân vật bị hạ.                                  |
| **atk**       | Sát thương vật lý/phép cơ bản. Ảnh hưởng damage kỹ năng.          |
| **def**       | Giảm sát thương nhận vào.                                         |
| **spd**       | Tốc độ hành động, quyết định thứ tự lượt (turn order).            |
| **crit**      | Tỉ lệ chí mạng %.                                                 |
| **critDmg**   | Sát thương cộng thêm khi chí mạng (1.5 = 150%).                   |
| **accuracy**  | Tỉ lệ chính xác, ảnh hưởng khi dùng kỹ năng debuff hoặc tấn công. |
| **evasion**   | Tỉ lệ né đòn.                                                     |
| **effectRes** | Kháng hiệu ứng: giảm tỉ lệ bị dính debuff (freeze/stun/poison…).  |

**Lưu ý:** vài game dùng công thức Damage = ATK × skillMultiplier × (1 ± RandomVariance).

---

# 📈 **4. growthCurves – Công thức tăng chỉ số theo cấp**

Mỗi chỉ số có:

* `base`: chỉ số ban đầu
* `perLevel`: tăng bao nhiêu mỗi cấp
* `curve`: dạng đường cong tăng trưởng

Ví dụ:

```json
"hp": {"base": 850, "perLevel": 42, "curve": "Linear"}
```

→ HP ở Level X = 850 + 42 × (X – 1)

`curve` có thể là:

* **Linear**: tăng đều theo cấp
* **Exponential**: cấp cao tăng mạnh hơn
* **Flat**: không tăng theo level (spd thường dùng kiểu này)

---

# 🎨 **5. assets – Tài nguyên đồ hoạ/hiệu ứng**

| Trường         | Mô tả                                    |
| -------------- | ---------------------------------------- |
| **prefab**     | Prefab nhân vật trong Unity.             |
| **animations** | Bộ animation pack.                       |
| **portrait**   | Ảnh đại diện nhân vật trong giao diện.   |
| **skillVFX**   | Đường dẫn thư mục chứa hiệu ứng kỹ năng. |

Dùng để liên kết frontend UI/Unity.

---

# 🗡️ **6. skillSet – Bộ kỹ năng nhân vật**

Mỗi nhân vật có 5 kỹ năng:

| Slot         | Ý nghĩa                                                           |
| ------------ | ----------------------------------------------------------------- |
| **basic**    | Đòn cơ bản (Auto attack).                                         |
| **skill1**   | Kỹ năng chủ động thứ nhất (Cooldown).                             |
| **skill2**   | Kỹ năng chủ động thứ hai (Cooldown).                              |
| **ultimate** | Chiêu cuối, tốn năng lượng/điều kiện kích hoạt.                   |
| **passive**  | Kỹ năng nội tại, luôn hiệu lực hoặc kích hoạt khi thỏa điều kiện. |

Các kỹ năng được liên kết tới Master Skill Config khác.

---

# 🔓 **7. unlockRequirements – Điều kiện mở khóa nhân vật**

| Trường              | Ý nghĩa                                                           |
| ------------------- | ----------------------------------------------------------------- |
| **defaultUnlocked** | Mặc định có sẵn hay phải mở khóa?                                 |
| **unlockMethod**    | Phương thức mở khóa: Gacha, Story, Tutorial, Event…               |
| **fragments**       | Số lượng mảnh ghép để summon nhân vật (nếu dùng fragment system). |

VD:

* SR cần 50 fragments
* SSR cần 80 fragments

---

# 🔱 **8. ascension – Hệ thống thăng cấp bậc**

Ascension = nâng bậc nhân vật (0 → 6 hoặc 0 → 5 tùy rarity).

| Trường           | Ý nghĩa                                     |
| ---------------- | ------------------------------------------- |
| **maxAscension** | Bậc tối đa.                                 |
| **materials**    | Danh sách item cần để tăng 1 bậc ascension. |

Ví dụ:

```json
{"itemId": "MAT_FIRE_ESSENCE", "count": 20}
```

Tăng Ascension thường:

* mở level cap (80 → 90 → 100…)
* nâng chỉ số lớn (bonus stats)
* mở skill hoặc tăng skill level cap

---

# 📚 **TÓM TẮT Ý NGHĨA THỰC TIỄN TRONG GAME**

## Hệ thống này giúp:

✔ Điều chỉnh sức mạnh nhân vật
✔ Tạo meta team, yếu tố khắc chế (element, role, tags)
✔ Điều chỉnh tốc độ tăng trưởng chỉ số (growthCurves)
✔ Cân bằng giữa các rarity
✔ Tạo lộ trình phát triển người chơi (Ascension, fragments)
✔ Kết nối file localization đa ngôn ngữ
✔ Liên kết asset Unity để load nhân vật vào game

---
