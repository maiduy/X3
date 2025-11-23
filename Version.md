**Kill-Switch (Cơ chế ngắt khẩn cấp)** và **Versioning (Quản lý phiên bản)** chính là bước chuyển hóa từ một hệ thống "tĩnh" sang một hệ thống **LiveOps chuyên nghiệp**. Đây là tấm khiên bảo vệ (Safety Net) giúp bạn ngủ ngon khi vận hành game.

Dưới đây là thiết kế kỹ thuật chi tiết bổ sung cho 2 cơ chế này vào kiến trúc Config-Driven.

-----

### 1\. Hệ thống Versioning & Manifest (Quản lý Phiên bản)

Thay vì Client tải từng file JSON rời rạc, hệ thống sẽ hoạt động dựa trên một file tổng **`Manifest.json`**. File này đóng vai trò là "Mục lục" và "Người gác cổng".

#### A. Thiết kế `Manifest.json`

File này được tải đầu tiên khi game khởi động (Bootstrap).

```json
{
  "manifestVersion": "1.5.0",      // Phiên bản của bộ dữ liệu hiện tại
  "minClientVersion": "1.2.0",     // Yêu cầu Client Code tối thiểu (để tránh crash do config dùng tính năng code chưa có)
  "env": "Production",             // Môi trường (Dev/Staging/Prod)
  "files": {
    "characters": {
      "path": "Config/v1.5/characters.json",
      "hash": "a1b2c3d4",          // MD5/SHA256 để kiểm tra tính toàn vẹn
      "size": 1024
    },
    "skills": {
      "path": "Config/v1.5/skills.json",
      "hash": "e5f6g7h8",
      "size": 2048
    },
    "killSwitch": {                // Kill-Switch luôn nằm trong manifest để load sớm nhất
      "path": "Config/v1.5/killswitch.json",
      "hash": "x9y8z7w6"
    }
  }
}
```

#### B. Quy trình tải (Download Flow)

1.  **Check Manifest:** Client tải `Manifest.json` từ CDN.
2.  **Version Check:** So sánh `minClientVersion` với `Application.version`.
      * Nếu `Client < minClientVersion`: Bắt buộc người chơi cập nhật App trên Store (Force Update UI).
3.  **Hash Check (Incremental Update):** Client so sánh hash của các file trong Manifest với hash của file đã cache trong máy.
      * Chỉ tải lại những file có hash thay đổi (Tiết kiệm băng thông 4G cho user).

-----

### 2\. Hệ thống Kill-Switch (Cơ chế Ngắt Khẩn Cấp)

Kill-Switch là một file cấu hình đặc biệt, được ưu tiên tải và xử lý trước tất cả logic game. Nó chứa danh sách đen (Blacklist) các tính năng hoặc nội dung đang bị lỗi.

#### A. Thiết kế `KillSwitch.json`

```json
{
  "maintenance": {
    "isActive": false,
    "message": "Server đang bảo trì để sửa lỗi nghiêm trọng.",
    "allowedIPs": ["192.168.1.5"] // Cho phép QA/Dev vào test dù đang bảo trì
  },
  "disabledFeatures": [
    "FEATURE_GUILD_WAR", // Tắt cả hệ thống Bang hội chiến
    "FEATURE_ADS_REWARD" // Tắt quảng cáo nếu mediation bị lỗi
  ],
  "disabledEntities": [
    "HERO_ARTHUR_BUGGY", // Tướng Arthur đang gây crash -> Ẩn khỏi game
    "ITEM_GLITCH_SWORD"  // Kiếm này đang bị lỗi dup đồ -> Không cho equip
  ],
  "disabledProducts": [
    "IAP_PACK_ERROR_PRICE" // Gói nạp đang hiển thị sai giá -> Ẩn khỏi shop
  ],
  "hotfixValues": { // Sửa nóng giá trị nhỏ
    "global_chat_enabled": false
  }
}
```

#### B. Implement trong Unity (C\#)

Sử dụng Pattern **Feature Gate (Cổng tính năng)**. Mọi hệ thống trước khi chạy phải "hỏi" Gatekeeper.

```csharp
public static class FeatureGate
{
    private static HashSet<string> _disabledSet = new HashSet<string>();

    // Hàm khởi tạo, đọc từ JSON KillSwitch
    public static void Initialize(KillSwitchConfig config) {
        _disabledSet.UnionWith(config.disabledFeatures);
        _disabledSet.UnionWith(config.disabledEntities);
        // ...
    }

    public static bool IsActive(string featureId) {
        // Nếu ID nằm trong danh sách cấm -> Trả về false
        if (_disabledSet.Contains(featureId)) return false;
        return true;
    }
}

// --- Ví dụ sử dụng trong UI Shop ---
public void ShowHeroInShop(string heroId) {
    if (!FeatureGate.IsActive(heroId)) {
        Debug.Log($"Hero {heroId} is kill-switched. Hiding from Shop.");
        return; // Không hiển thị tướng này
    }
    // Logic hiển thị bình thường...
}
```

-----

### 3\. Cập nhật vào Master Architecture

Để tích hợp 2 cơ chế này, chúng ta cập nhật lại bảng cấu hình và quy trình pipeline.

#### A. Cập nhật Bảng Master Config

| Phân Hệ | File Config | Chức năng Mới (Versioning & Safety) |
| :--- | :--- | :--- |
| **BOOTSTRAP** | `Manifest.json` | **Root of Trust.** Kiểm soát version, bắt buộc update app, quản lý file hash. |
| **BOOTSTRAP** | `KillSwitch.json` | **Emergency Brake.** Tắt nóng tính năng/tướng/IAP ngay lập tức khi có sự cố. |
| **CORE** | `Characters.json` | Thêm trường `minClientVer` cho từng tướng (Tướng mới dùng shader mới, app cũ không load được). |
| **LIVEOPS** | `Maintenance.json` | Cấu hình thông báo bảo trì, whitelist IP cho tester. |

#### B. Chiến lược "Fail-Safe" (An toàn là trên hết)

Khi thiết kế code đọc Config, hãy áp dụng nguyên tắc:

1.  **Default to Safe:** Nếu không tải được `KillSwitch.json` (do mạng lỗi), hãy sử dụng phiên bản cache gần nhất. Nếu không có cache, hãy cho game chạy nhưng *tắt* các tính năng nhạy cảm (Shop/Ads).
2.  **Graceful Degradation:** Nếu Config của một Tướng bị lỗi cú pháp (Syntax Error), hệ thống chỉ nên **bỏ qua tướng đó** và log lỗi, thay vì làm crash cả game lúc loading.
      * *Try-Catch block* quanh vòng lặp load từng Entity.

### 4\. Lợi ích Thực tế

  * **Tình huống 1:** Bạn ra mắt tướng mới "Rồng Lửa". Người chơi phát hiện bug: Dùng chiêu cuối làm crash game đối thủ.

      * *Cách cũ:* Gỡ game khỏi store, fix bug, chờ review (mất 2 ngày). User chửi bới.
      * *Cách mới:* Update `KillSwitch.json` -\> Thêm `"HERO_FIRE_DRAGON"` vào `disabledEntities`. 5 phút sau, tướng này biến mất khỏi màn hình chọn tướng. User vẫn chơi game bình thường.

  * **Tình huống 2:** Bạn update logic tính damage mới trong code C\# (v1.2). Config mới (v1.2) dựa trên logic này.

      * *Vấn đề:* Người chơi chưa update app (v1.1) tải nhầm config v1.2 -\> Crash.
      * *Giải pháp:* `Manifest.json` có `minClientVersion: "1.2.0"`. App v1.1 đọc thấy dòng này -\> Hiện popup: "Vui lòng cập nhật game để tiếp tục".

Đây là mảnh ghép cuối cùng để biến hệ thống Config-Driven của bạn trở nên **Robust (Mạnh mẽ)** và sẵn sàng cho môi trường Production khốc liệt.