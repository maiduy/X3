
---

# 📦 **Game Client Manifest Configuration**

File này định nghĩa **cấu hình tải về và cập nhật dữ liệu game từ CDN**, bao gồm tất cả các tệp dữ liệu quan trọng, chiến lược cập nhật, cache, và rollback.

---

## 🧩 **1. Metadata chung**

| Trường           | Mô tả                                     |
| ---------------- | ----------------------------------------- |
| manifestVersion  | Phiên bản manifest, ví dụ "1.0.0"         |
| minClientVersion | Phiên bản client tối thiểu yêu cầu        |
| environment      | Môi trường, ví dụ "Production"            |
| lastUpdated      | Thời điểm manifest được cập nhật lần cuối |
| cdnBaseUrl       | URL cơ sở CDN chứa các file cấu hình      |

✅ Giúp client biết **phiên bản manifest & nơi tải dữ liệu**.

---

## 🧩 **2. Files (Danh sách file game)**

Mỗi file có các thuộc tính:

| Thuộc tính  | Mô tả                                                    |
| ----------- | -------------------------------------------------------- |
| path        | Đường dẫn file trên CDN                                  |
| hash        | MD5 hash để kiểm tra tính toàn vẹn                       |
| size        | Kích thước file (byte)                                   |
| priority    | Thứ tự tải / độ ưu tiên (1 là cao nhất)                  |
| critical    | true → bắt buộc tải trước, game không thể chạy nếu thiếu |
| description | Mô tả ngắn về nội dung file                              |

**Ví dụ các loại file:**

1. **System & Feature**: KillSwitch, Maintenance, FeatureFlags, SegmentConfig, GlobalModifiers
2. **Core Game Data**: Characters, Items, GrowthCurves, GearSets
3. **Gameplay**: Skills, BattleRules, AIProfiles
4. **Content**: Levels, ArenaConfig, Banners, ShopProducts, Economy
5. **LiveOps**: EventSchedule, Quests
6. **Analytics & Tracking**: Analytics.json
7. **Localization**: Localization, LocalizationKeys, languageEn/zh/ko

✅ Giúp client **tải đúng dữ liệu cần thiết theo thứ tự ưu tiên**, bao gồm dữ liệu cốt lõi, gameplay, event, localization, và analytics.

---

## 🧩 **3. Update Strategy (Chiến lược cập nhật)**

| Trường                | Mô tả                                          |
| --------------------- | ---------------------------------------------- |
| type                  | Incremental → cập nhật chỉ những file thay đổi |
| checkInterval         | Khoảng thời gian kiểm tra bản mới (giây)       |
| forceUpdateOnLaunch   | true → bắt buộc cập nhật khi mở game           |
| allowBackgroundUpdate | Cho phép cập nhật ngầm                         |
| maxRetryAttempts      | Số lần thử lại nếu tải thất bại                |
| retryDelay            | Khoảng thời gian giữa các lần thử lại (ms)     |

✅ Tối ưu hóa **tốc độ cập nhật & giảm gián đoạn trải nghiệm người chơi**.

---

## 🧩 **4. Caching**

| Trường             | Mô tả                               |
| ------------------ | ----------------------------------- |
| enabled            | Bật/tắt cache local                 |
| maxCacheSize       | Dung lượng tối đa (byte)            |
| cacheDuration      | Thời gian cache còn hiệu lực (giây) |
| compressionEnabled | Dùng nén để tiết kiệm băng thông    |

✅ Giúp **giảm tải CDN & tăng tốc khởi động game**.

---

## 🧩 **5. Validation**

| Trường               | Mô tả                                               |
| -------------------- | --------------------------------------------------- |
| verifyHash           | Kiểm tra hash MD5 file để đảm bảo dữ liệu chính xác |
| verifySignature      | Kiểm tra chữ ký (hiện đang false)                   |
| allowFallbackToCache | Cho phép dùng cache nếu CDN lỗi                     |
| strictMode           | true → từ chối tất cả file không hợp lệ             |

✅ Đảm bảo **tính toàn vẹn và an toàn dữ liệu**.

---

## 🧩 **6. Rollback**

| Trường               | Mô tả                                           |
| -------------------- | ----------------------------------------------- |
| enabled              | Cho phép rollback nếu cập nhật lỗi              |
| keepPreviousVersions | Giữ lại bao nhiêu phiên bản cũ để rollback      |
| autoRollbackOnError  | Tự động quay lại phiên bản trước khi lỗi xảy ra |

✅ Giúp **giảm thiểu rủi ro lỗi update trên client**.

---

### ✅ **Tóm tắt**

* Đây là **manifest tổng quát của game client**, điều phối:

  * Tải dữ liệu game từ CDN
  * Kiểm tra tính toàn vẹn & hash
  * Chiến lược cập nhật incremental / background
  * Caching và nén dữ liệu
  * Rollback tự động nếu lỗi
* Bao gồm tất cả **file hệ thống, gameplay, liveops, event, localization, analytics** với ưu tiên & tính quan trọng.

---

