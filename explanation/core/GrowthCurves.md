
---

# 🚀 **1. TỔNG QUAN JSON NÀY DÙNG ĐỂ LÀM GÌ?**

Đây là **Master Growth Curve Configuration** – dùng trong game RPG, Gacha Battle, Turn-Based hoặc ARPG.

JSON bao gồm:

### ✔ **4 nhóm Stat Curves cho nhân vật (character growth curves)**

* CURVE_WARRIOR_S_TIER
* CURVE_MAGE_S_TIER
* CURVE_SUPPORT_A_TIER
* CURVE_COMMON_B_TIER

Dùng để tính **HP / ATK / DEF / SPD từ Level 1 → Max Level** dựa trên **base stat**.

### ✔ **2 nhóm Exponential Curves (cho EXP và Gold)**

* EXP Required per Level
* Equipment Enhancement Cost

Dùng cho:

* Cấp nhân vật → cần bao nhiêu EXP?
* Tăng cấp trang bị → tốn bao nhiêu vàng?

---

# 🧩 **2. GIẢI THÍCH STAT CURVES (curves[])**

Mỗi curve gồm:

```json
"id": "...",
"maxLevel": 80,
"stats": {
  "hp": {...},
  "atk": {...},
  "def": {...},
  "spd": {...}
}
```

---

# ⭐ **2.1 Công thức (formula) – Ý nghĩa thực tế**

Ví dụ:

```
"formula": "base * (1 + (level - 1) * 0.08)"
```

Nghĩa là:

```
Stat(level) = base × [1 + tăng trưởng mỗi level × (level - 1)]
```

**base** được định nghĩa trong Character Data:

```
baseHp = 1000
baseAtk = 50
```

Ví dụ Warrior ATK growth 6%:

* LV1 → 50
* LV40 → 50 × (1 + 39 × 0.06) = 50 × 3.34 = 167
* LV80 → 50 × 5.74 = 287

=> **Breakpoints** là giá trị mẫu (sample) để check đúng formula.

---

# ⭐ **2.2 Breakpoints – dùng để làm gì?**

Ví dụ:

```
{"level": 40, "multiplier": 4.12}
```

Breakpoints dùng để:

### ✔ Validate công thức đúng

Trong pipeline build data, hệ thống kiểm tra:

```
calculated = formula(level=40)
expected = multiplier * base
if abs(calculated-expected) > tolerance => báo lỗi
```

### ✔ Tối ưu tính toán client

Unity có thể dùng **lookup table** thay vì tính công thức.

---

# 📘 **3. GIẢI THÍCH CHI TIẾT TỪNG CURVE**

---

# 🔥 **CURVE_WARRIOR_S_TIER – SSR Warrior**

### HP Growth

+8% mỗi level → rất trâu.

### ATK Growth

+6% → trung bình, thiên về tank/damage hybrid.

### DEF Growth

+7% → phòng thủ cao.

### SPD

Không tăng theo level.

=> **Designed for bruiser / frontline fighter**.

---

# ❄️ **CURVE_MAGE_S_TIER – SSR Mage**

### HP Growth

+6% → máu trung bình.

### ATK Growth

+8% → cao nhất toàn bộ.

### DEF Growth

+5% → yếu.

SPD cố định.

=> **Thiết kế chuẩn burst DPS**.

---

# 💚 **CURVE_SUPPORT_A_TIER – SR Support**

### HP

+7% → khá trâu.

### ATK

+5% → thấp.

### DEF

+6% → ổn.

=> Support khỏe nhưng sát thương yếu.

---

# ⚪ **CURVE_COMMON_B_TIER – R nhân vật thường**

### HP: +5.5%

### ATK: +5%

### DEF: +5%

### Max Level = 70

=> Nhân vật hạng R yếu nhất → dùng cho tân thủ.

---

# 🔢 **4. GIẢI THÍCH EXPONENTIAL CURVES (exponentialCurves[])**

---

# 📌 **4.1 EXP Required per Level**

Formula:

```
floor(100 * pow(1.1, level - 1))
```

Đây là **EXP tăng theo cấp số nhân**, mỗi level cần EXP cao hơn ~10%.

Breakpoints:

* LV1: 100
* LV10: 236
* LV20: 673
* LV40: 4526
* LV80: 204840

Đồ thị EXP → hình chữ J.

---

# 🟡 **4.2 Equipment Enhancement Gold Cost**

```
floor(500 * pow(1.15, level))
```

Tăng theo 15% mỗi level enhance.

Ví dụ:

* LV1 → 575
* LV10 → 2027
* LV15 → 4092

=> Dùng cho tăng cấp trang bị (equipment upgrade system).

---

# 🧠 **5. CÁCH UNITY SỬ DỤNG CONFIG NÀY**

### 1️⃣ Khi load Character Data

```csharp
float hp = GrowthCurve.Eval("CURVE_WARRIOR_S_TIER", "hp", baseHp, level);
```

### 2️⃣ Khi nhân vật lên cấp

* Client tính theo formula
* Server validate bằng formula hoặc breakpoints

### 3️⃣ Khi hiển thị UI "Next Level Stats"

Unity dùng công thức hoặc lookup table pre-calculated.

### 4️⃣ Khi lưu MongoDB

Bạn có thể lưu:

```
curveId: "CURVE_WARRIOR_S_TIER"
lvl: 55
currentExp: 12994
```

---
