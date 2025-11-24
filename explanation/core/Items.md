* **Cấu trúc tổng thể JSON**
* **Ý nghĩa từng nhóm thuộc tính**
* **Cách sử dụng trong Unity + Server**
* **Cách tính stat & roll**
* **Hệ thống set bonus, enhancement, consumable formula, materials, currency, gacha ticket**
* **Thiết kế chuẩn để load qua Addressables & MongoDB**

---

# 📘 1. TỔNG QUAN JSON ITEMS CONFIG

File này là **Master Item Database** chứa mọi item trong game:

| Nhóm                       | Mô tả                                       |
| -------------------------- | ------------------------------------------- |
| Weapon / Armor / Accessory | Trang bị có level, stats, roll, enhancement |
| Consumable                 | Item sử dụng (heal, buff…)                  |
| Material                   | Dùng cho craft, ascension, enhancement      |
| Currency                   | Tiền mềm, tiền premium                      |
| Special                    | Gacha Ticket, event items                   |

Cấu trúc chuẩn 8 nhóm:

1. **meta** → định danh, đặc tính chung
2. **baseStats** → chỉ số cố định
3. **statRollRange** → random stat (min-max)
4. **subStats** → secondary random stats
5. **setBonus / uniqueEffect** → hiệu ứng trang bị
6. **enhancement** → nâng cấp trang bị
7. **usageRules** → cách sử dụng item
8. **assets** → icon, prefab, vfx

---

# 🔥 2. GIẢI THÍCH CHI TIẾT TỪNG ITEM

---

# 🗡️ **ITEM_SWORD_FLAME (SSR Weapon – Sword)**

## 1️⃣ meta

* rarity: **SSR** → ảnh hưởng roll RNG, drop rate
* maxLevel: **90**
* slot: **MainHand**

## 2️⃣ baseStats

```
atk: 245
critRate: 0.12 (12%)
critDmg: 0.35 (35%)
```

Đây là giá trị **không thay đổi**, áp dụng cho LV1 trước enhancement.

## 3️⃣ statRollRange (roll khi drop)

Ví dụ ATK roll:

```
min 220 → max 270
```

=> Trang bị khi rơi ra sẽ roll ATK khác nhau trong khoảng này.

## 4️⃣ subStats

* maxSubStats = 4
* weighted pool:

Ví dụ:

```
hp: weight 1000 → tỉ lệ cao nhất
atk%: weight 800
spd: weight 600
effectHit: 400
```

Hệ thống random theo trọng số:

```
weightTotal = 1000 + 800 + 600 + 400 = 2800
```

=> hp = ~35% chance.

## 5️⃣ setBonus – Set INFERNO

### Set 2 mảnh:

* Fire DMG +15%

### Set 4 mảnh:

* Sau khi cast skill → buff ATK 25% trong 3 turn

Định nghĩa buff "BUFF_INFERNO_ATK" sẽ nằm ở file khác (buff.json).

## 6️⃣ enhancement

Nâng tối đa +15.

Mỗi mốc level:

* Level 1 → gold 1000 + Ore 5
* Level 5 → gold 5000 + Ore 10
* Level 10 → ...
* Level 15 → Legendary Ore 3

Unity/Server tính enhancement stat theo growth curve hoặc linear table (ở đây thiếu bảng bonus stat → sẽ thêm ở master khác).

## 7️⃣ assets

Dùng cho Addressables:

* Prefab
* Icon

---

# 🧊 **ITEM_ARMOR_ICE_PLATE (SR Armor – Heavy)**

### BaseStats:

```
def: 180
hp: 850
effectRes: 0.15
```

### Roll Range:

* DEF: 160 → 200
* HP: 750 → 950
* EffectRes: 10–20%

### Substats:

3 random stats, không phải 4 như vũ khí SSR.

### SetBonus – SET_GLACIER

**2 pieces** → DEF +20%
**4 pieces** → Khi bị đánh, 50% Freeze attacker 1 turn

Có trigger:

```
OnHit → WhenReceiveDamage
chance = 0.5
```

---

# 🐉 **ITEM_ACCESSORY_DRAGON_AMULET (SSR Accessory – Amulet)**

Không có set bonus → thay bằng **uniqueEffect**.

### Base Stats:

* atk: 120
* hp: 500
* critDmg: 40%

### Unique Effect:

```
Elemental DMG +20%
If HP > 80% → ATK +15%
```

=> Item này là dạng “God-tier accessory”.

### Substats:

max 4, substats mạnh hơn giáp.

---

# ❤️ **ITEM_CONSUMABLE_HP_POTION**

Loại: Consumable
maxLevel: 1

### effect:

```
type: InstantHeal
value: 500
formula: "500 + (target.maxHp * 0.15)"
```

Công thức heal:

```
heal = 500 + 15% maxHP
```

### usageRules:

* usableInCombat = true
* cooldown = 5s
* maxStack 99
* consumeOnUse = true

### assets:

icon + VFX dùng khi uống potion.

---

# 🐲 **ITEM_MATERIAL_DRAGON_SCALE**

Vật liệu Ascension SSR.

### usageRules

* tradeable: false
* sellable: true (price = 5000)

### sources:

* Drop from BOSS Ancient Dragon (15%)
* Guild Shop
* Event dragon raid

---

# 💰 **ITEM_CURRENCY_GOLD**

Soft currency.

* maxStack: cực lớn
* không trade, không bán
* không model, chỉ icon

---

# 💎 **ITEM_CURRENCY_GEM**

Premium currency.

Có iapMapping:

```
canPurchase: true
exchangeRate: 1.0  (1$ = 1 gem hoặc quy tắc riêng)
```

---

# 📜 **ITEM_SUMMON_SCROLL**

Ticket Gacha.

### gachaMapping:

```
validBanners: [Standard, FireDragon]
pullCount: 1
```

Dùng cho single-pull.

---

# 🧠 3. CÁCH UNITY SỬ DỤNG CONFIG NÀY

### 1. Load vào Addressables

Dùng model:

```
class ItemDefinition
{
   public string id;
   public Meta meta;
   public Stats baseStats;
   public RollRanges statRollRange;
   ...
}
```

### 2. Khi tạo item drop:

* random statRollRange
* random substats theo weight
* apply enhancement → cấp 0

### 3. Khi tăng cấp trang bị

* lấy bảng enhancement
* cộng vào baseStats

### 4. Khi trigger set bonus

* đọc setId
* apply 2pc/4pc effect

### 5. Khi sử dụng Consumable

Server tính:

```
healValue = 500 + (maxHp * 0.15)
```

### 6. Khi xử lý Gacha

Kiểm tra “validBanners”.

---