
---

# ⚙️ **Maintenance & Compensation Config**

File này quản lý **lịch bảo trì, trạng thái bảo trì hiện tại và cơ chế bồi thường** cho người chơi.

---

## 🧩 **1. Maintenance Schedule (Lịch bảo trì dự kiến)**

| Trường            | Mô tả                                                                      |
| ----------------- | -------------------------------------------------------------------------- |
| isScheduled       | true → có lịch bảo trì dự kiến                                             |
| plannedStart      | thời gian dự kiến bắt đầu bảo trì                                          |
| plannedEnd        | thời gian dự kiến kết thúc bảo trì                                         |
| estimatedDuration | thời lượng ước tính (giây), ví dụ 7200 = 2 giờ                             |
| notifyBeforeStart | gửi thông báo trước khi bắt đầu (giây), ví dụ 3600 = 1 giờ                 |
| allowEarlyEntry   | true → người chơi có thể vào game trước thời gian dự kiến kết thúc bảo trì |

✅ Đây dùng để **lập kế hoạch và thông báo trước** cho người chơi.

---

## 🧩 **2. Current Maintenance (Bảo trì hiện tại)**

| Trường           | Mô tả                                                    |
| ---------------- | -------------------------------------------------------- |
| isActive         | true → bảo trì đang diễn ra                              |
| startTime        | thời gian bắt đầu bảo trì hiện tại                       |
| endTime          | thời gian kết thúc dự kiến                               |
| type             | Loại bảo trì: Scheduled / Emergency                      |
| severity         | Mức độ: Normal / High / Critical                         |
| reason           | Lý do bảo trì, ví dụ: "Regular maintenance"              |
| affectedServices | Danh sách dịch vụ bị ảnh hưởng (PvP, Gacha, Chat, Shop…) |

✅ Giúp **theo dõi trạng thái server & dịch vụ bị tác động**.

---

## 🧩 **3. Compensation (Bồi thường người chơi)**

| Trường         | Mô tả                                                           |
| -------------- | --------------------------------------------------------------- |
| enabled        | true → bồi thường được kích hoạt                                |
| autoCalculate  | true → tự động tính lượng bồi thường dựa trên thời gian bảo trì |
| baseRewards    | danh sách phần thưởng cơ bản, ví dụ: 100 Gem                    |
| deliveryMethod | Mailbox → gửi trực tiếp vào hộp thư game                        |
| expirationDays | số ngày người chơi có thể nhận phần thưởng, ví dụ 7 ngày        |

✅ Giúp **tự động gửi bồi thường cho người chơi bị ảnh hưởng**.

---

## 🧩 **4. Monitoring**

| Trường              | Mô tả                                                         |
| ------------------- | ------------------------------------------------------------- |
| healthCheckInterval | khoảng thời gian (giây) kiểm tra tình trạng server, ví dụ 60s |
| autoDetectIssues    | true → tự động phát hiện các vấn đề trong quá trình bảo trì   |

✅ Hỗ trợ **LiveOps giám sát và phản ứng nhanh** nếu có sự cố xảy ra.

---

