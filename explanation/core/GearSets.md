

---

# ✅ **1. CẤU TRÚC CHUNG CỦA “SETS”**

Mỗi set trang bị gồm:

| Trường            | Ý nghĩa                                                                 |
| ----------------- | ----------------------------------------------------------------------- |
| **id**            | Mã duy nhất của bộ trang bị.                                            |
| **meta**          | Thông tin mô tả set: tên, mô tả, icon, element, tier.                   |
| **pieces**        | Hiệu ứng khi trang bị 2 món hoặc 4 món.                                 |
| **requiredSlots** | Những loại trang bị phải có để set này hợp lệ (Weapon, Chest, Gloves…). |

---

# ✅ **2. GIẢI THÍCH TỪNG TRƯỜNG TRONG meta**

### **meta**

* **nameKey**: Key đọc từ bảng multi-language (EN/CH/KR).
* **descriptionKey**: Key mô tả.
* **icon**: Icon hiển thị trong UI.
* **element**: Thuộc tính nguyên tố của bộ set.
* **tier**: Độ hiếm (R / SR / SSR).

---

# ✅ **3. GIẢI THÍCH TỪNG KIỂU HIỆU ỨNG TRONG SET**

Dưới đây là giải thích đầy đủ tất cả **effects** xuất hiện.

---

## ⭐ **ElementalDmgBonus**

```
"type": "ElementalDmgBonus"
```

* Tăng sát thương của nguyên tố nhất định (Fire / Ice / Dark…)
* Chỉ áp dụng cho skill có thuộc tính cùng element.

---

## ⭐ **OnSkillUse**

```
"type": "OnSkillUse", "trigger": "AfterSkillCast"
```

* Kích hoạt khi nhân vật **dùng 1 skill xong**.
* Thường để triển khai buff tấn công, bắn hiệu ứng,…

---

## ⭐ **Buff (stat modifier)**

```
"stat": "atk%", "value": 0.25
```

* Tăng SATK theo % (tức ATK tăng 25% của ATK cơ bản + ATK bonus).
* Có **duration** và **stack**:

  * **duration**: số lượt tồn tại.
  * **maxStacks**: số lần cộng dồn tối đa.
  * **stackBehavior = Independent**: mỗi stack chạy timer riêng.

---

## ⭐ **StatBonus**

```
"type": "StatBonus", "stat": "def%", "value": 0.20
```

* Buff tĩnh, tăng % chỉ số (ATK, DEF, SPD, effectRes…).

---

## ⭐ **OnHit**

```
"type": "OnHit", "trigger": "WhenReceiveDamage"
```

* Trigger khi **bị tấn công**.
* Có thể kèm:

  * **chance**: tỉ lệ xảy ra (0.5 = 50%)
  * **cooldown**: số lượt chờ trước khi kích hoạt lại.

---

## ⭐ **ApplyDebuff**

```
"type": "ApplyDebuff", "id": "DEBUFF_FREEZE"
```

* Áp debuff lên mục tiêu:

  * Attacker → mục tiêu là kẻ tấn công.
  * Self → chính bản thân.

---

## ⭐ **Lifesteal**

```
"type": "Lifesteal", "value": 0.10
```

* Hồi máu theo % sát thương gây ra:

  * 10% hoặc 25% khi vào điều kiện đặc biệt.

---

## ⭐ **ConditionalBuff**

```
"condition": "HP_BELOW_50"
```

* Chỉ kích hoạt khi điều kiện đúng:

  * HP dưới 50%
  * HP trên 80%
  * Crit thành công
  * Nhận debuff…

---

## ⭐ **IncreaseATB**

```
"type": "IncreaseATB", "value": 0.15
```

* Tăng thanh hành động (Action Time Bar).
* 1.0 = tượt 1 lượt luôn.
* 0.15 = nhận trước 15% ATB → hành động sớm hơn.

---

## ⭐ **Counterattack**

```
"type": "Counterattack", "damageMultiplier": 0.75
```

* Phản đòn khi bị đánh.
* **damageMultiplier**: sát thương của đòn phản → 75% ATK.
* **critRateBonus**: thêm CRIT cho riêng đòn phản.

---

## ⭐ **ApplyBuff**

```
{
  "type": "ApplyBuff",
  "id": "BUFF_IMMUNITY",
  "duration": 2
}
```

* Gán buff miễn dịch / buff ATK / buff shield…

---

# ✅ **4. GIẢI THÍCH SET THEO NGỮ NGHĨA GAMEPLAY**

---

# 🔥 **SET_INFERNO (Fire – SSR)**

### **2 món – Blazing Power**

* +15% sát thương nguyên tố lửa → rất mạnh cho DPS Fire.

### **4 món – Phoenix’s Fury**

* Sau khi dùng skill:

  * +25% ATK, tồn tại 3 lượt.
  * Cộng dồn tối đa 3 stack → **75% ATK bonus**.
  * stack độc lập → duy trì rất lâu.

=> Set này thiết kế cho Fire DPS burst.

---

# ❄️ **SET_GLACIER (Ice – SR)**

### **2 món – Frozen Armor**

* +20% DEF → cực phù hợp cho tanker/support Ice.

### **4 món – Glacial Retaliation**

* Khi bị đánh:

  * 50% đóng băng mục tiêu.
  * CD 3 lượt.

=> Set counter-control, phù hợp tank Ice.

---

# 🩸 **SET_LIFESTEAL (Dark – SSR)**

### **2 món – Vampiric Touch**

* Hút máu 10%.

### **4 món – Blood Frenzy**

* Khi HP < 50%:

  * Lifesteal tăng lên 25%.
  * +30% ATK.

=> Set dành cho DPS liều mạng, high-risk high-reward.

---

# 🌪 **SET_SPEED (Wind – SR)**

### **2 món – Swift Strike**

* +25% Speed → tăng lượt hành động.

### **4 món – Blitz Assault**

* Vào trận:

  * +15% ATB.

=> Set meta cho Support/Assassin.

---

# ⚔️ **SET_COUNTER (Physical – SSR)**

### **2 món – Riposte**

* 15% phản đòn.
* 75% damage.

### **4 món – Perfect Parry**

* Tổng tỉ lệ phản đòn = 50%
* Sát thương phản đòn = 100%
* +30% CRIT Rate riêng cho đòn phản.

=> Set phản damage cực mạnh cho Tank/DPS.

---

# ✨ **SET_IMMUNITY (Light – SR)**

### **2 món – Cleansing Light**

* +20% Effect Resistance.

### **4 món – Divine Protection**

* Đầu trận:

  * Nhận buff “Immunity” 2 lượt (không thể bị debuff).

=> Set meta cho PvP và Support.

---

# ✅ **5. GIẢI THÍCH TẤT CẢ BUFFS**

---

# 🔥 **BUFF_INFERNO_ATK**

* +25% ATK theo dạng modifier.
* Có hiệu ứng VFX màu đỏ cam (#FF4500).

---

# 🛡 **BUFF_IMMUNITY**

* Miễn nhiễm toàn bộ debuff (Freeze, Poison, Bleed…)
* Giá trị 1.0 = 100% chống debuff.

---

# ❄️ **DEBUFF_FREEZE**

* “DisableAction”: không thể:

  * Move
  * Attack
  * Cast Skill
* Duration 1 turn.
* Màu xanh băng (#00BFFF).

---
