
---

# 📘 **Giải thích cấu trúc Game Analytics Master Config**

File JSON trên là **Master Analytics Configuration**. Nó định nghĩa:

### ✓ 1. Bộ **Metrics** (đo lường)

### ✓ 2. Bộ **Dashboards** (bảng điều khiển)

### ✓ 3. **Luật thu thập – aggregation – storage – alerting**

### ✓ 4. **Cấu hình export sang CSV / MongoDB**

### ✓ 5. Hoạt động như *“Game Telemetry Spec”* để client, server và BI dùng chung.

---

# 🧩 **1. Schema tổng quan**

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "version": "1.0.0",
  "lastUpdated": "2025-11-23T00:00:00Z",
  "metrics": [...],
  "dashboards": [...],
  "exportConfig": {...}
}
```

Ý nghĩa:

* **$schema** → chuẩn validate file.
* **version** → versioning của analytics system.
* **lastUpdated** → hệ thống pipeline sẽ reload khi timestamp thay đổi.
* **metrics** → định nghĩa từng metric.
* **dashboards** → nhóm các metric vào màn hình giám sát.
* **exportConfig** → cấu hình tự động export.

---

# 📊 **2. Cấu trúc 1 metric (chuẩn Modular Analytics Config)**

Một metric bao gồm 5 phần chính:

### 1. `id`

→ định danh duy nhất, dùng trong Dashboard, ETL, MongoDB, client events.

### 2. `meta`

Thông tin mô tả để BI và Developer dùng:

| Thuộc tính  | Ý nghĩa                                                   |
| ----------- | --------------------------------------------------------- |
| name        | Tên metric                                                |
| description | Ý nghĩa, công thức                                        |
| category    | Nhóm (Engagement, Retention, Monetization, Economy, PvP…) |
| priority    | Mức độ quan trọng → dùng trong Alerting & SLA             |

---

### 3. `collection`

Luật thu thập dữ liệu từ Game Events.

| Trường      | Giải thích                                  |
| ----------- | ------------------------------------------- |
| event       | Event gốc: UserLogin, SessionEnd, Purchase… |
| frequency   | RealTime / Daily                            |
| dataType    | Count, Amount, Percentage, Duration…        |
| aggregation | UniqueCount, Sum, Average, RetentionCohort… |
| dimensions  | Phân nhóm: Date, Platform, Region, StageId… |

---

### 4. `storage`

```json
"storage": {
  "database": "MongoDB",
  "collection": "engagement_metrics",
  "retentionDays": 365
}
```

* database → MongoDB
* collection → tên collection lưu metric
* retentionDays → số ngày giữ dữ liệu

---

### 5. `alerts`

Nếu metric quan trọng → bật Alert.

Ví dụ:

```json
{
  "type": "Percentage",
  "value": -10,
  "comparison": "DayOverDay"
}
```

→ Khi DAU giảm 10% so với ngày trước → cảnh báo.

---

# 🧩 **3. Diễn giải đầy đủ *tất cả metrics* của bạn**

---

## **ENGAGEMENT**

### **METRIC_DAU**

* Đếm số user đăng nhập trong ngày.
* Real-time, UniqueCount.
* Alert khi giảm >10% Day-over-Day.

### **METRIC_MAU**

* Đếm user unique trong 30 ngày.
* Daily aggregation.
* Alert khi giảm >5% MoM.

### **METRIC_SESSION_LENGTH**

* Thời lượng phiên chơi (phút).
* Real-time → từ event SessionEnd.
* Không bật alert.

---

## **RETENTION**

### **METRIC_RETENTION_D1**

* % user quay lại ngày thứ 1 sau ngày cài.
* Daily, type: Percentage, aggregation: RetentionCohort.
* Alert khi dưới 40%.

### **METRIC_RETENTION_D7**

* Tương tự D1 nhưng 7 ngày.
* Alert khi dưới 20%.

---

## **MONETIZATION**

### **METRIC_ARPU**

* Doanh thu trung bình theo **toàn bộ user** active.
* Daily, aggregation AveragePerUser.
* Alert khi Week-over-week giảm >15%.

### **METRIC_ARPPU**

* Doanh thu trung bình theo **paying users**.
* Không bật alert.

### **METRIC_CONVERSION_RATE**

* % người dùng có ít nhất 1 purchase.
* Alert nếu giảm 20% Week-over-week.

---

## **ECONOMY**

### **METRIC_GACHA_PULL_COUNT**

* Tổng số lượt quay gacha.
* Real-time.
* Không alert.

### **METRIC_CURRENCY_EARNED**

* Tổng tiền user kiếm được.
* Tính theo Amount.
* Không alert.

### **METRIC_CURRENCY_SPENT**

* Tổng tiền user tiêu.
* Tính theo Sink.

---

## **PROGRESSION**

### **METRIC_STAGE_COMPLETION**

* Tỷ lệ qua màn = completed / attempts.
* Real-time → StageEnd.
* Alert nếu <30% theo từng Stage.

---

## **PVP**

### **METRIC_ARENA_PARTICIPATION**

* % user tham gia PVP.
* Daily.
* Không alert.

---

## **TECHNICAL**

### **METRIC_CRASH_RATE**

* % phiên chơi bị crash.
* Alert khi >2%.

### **METRIC_LOAD_TIME**

* Thời gian load scene trung bình (ms).
* Alert khi >5000ms (5 giây).

---

# 🧭 **4. Dashboards**

### **DASHBOARD_OVERVIEW**

Bao gồm:

* DAU
* MAU
* Retention D1 / D7
* ARPU
* Conversion Rate

→ Dành cho CEO / Game Director.

### **DASHBOARD_MONETIZATION**

* ARPU
* ARPPU
* Conversion Rate
* Gacha Pull Count

→ Dành cho Economy & Monetization team.

### **DASHBOARD_ECONOMY**

* Currency Earned
* Currency Spent
* Gacha Pull Count

→ Dành cho Economy Balancing.

---

# 📤 **5. Export Config**

```json
"exportConfig": {
  "enabled": true,
  "format": "CSV",
  "schedule": "Daily",
  "destination": "MongoDB",
  "compression": true
}
```

Giải thích:

* Hệ thống tự sinh CSV theo schedule (Daily).
* Lưu vào MongoDB (GridFS hoặc collection raw_export).
* Nén (GZIP) để giảm dung lượng.

---

# 🏗 **6. Tóm tắt giá trị của cấu hình này**

* Chuẩn hóa toàn bộ telemetry → client, server, ETL sử dụng thống nhất.
* Dễ mở rộng (chỉ cần thêm metric mới vào file).
* Dễ scale theo event-driven architecture.
* Tránh hard-code metric trong code.
* Tối ưu BI, báo cáo và cảnh báo real-time.

---
