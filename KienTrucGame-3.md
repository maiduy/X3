Đây là một yêu cầu rất cụ thể và phản ánh một kiến trúc game server hiện đại, có khả năng mở rộng cao (high scalability) và phân tách nhiệm vụ rõ ràng (separation of concerns).

Việc sử dụng **Microservices**, **NATS JetStream** làm xương sống giao tiếp sự kiện, kết hợp với sức mạnh lưu trữ linh hoạt của **MongoDB** và tốc độ của **Redis**, là một lựa chọn kiến trúc tuyệt vời cho một game "Team Battle" thời gian thực.

Dưới đây là sơ đồ kiến trúc và phân tích chi tiết từng thành phần.

### Sơ đồ Kiến trúc Fullstack Game Server (Mermaid Diagram)

```mermaid
graph TD
    %% Actors & Clients
    Client[Game Client Mobile/PC]
    AdminUI[Admin Portal\n(For Config/LiveOps Config)]

    %% Edge Layer
    subgraph "Edge Layer & Ingress"
        LB[Load Balancer]
        Gateway[API Gateway / WebSocket Gateway]
    end

    %% Communication Backbone
    subgraph "Event Bus & Streaming Backbone"
        NATS[NATS JetStream Cluster\n(Pub/Sub, Durable Streams)]
    end

    %% Real-time Gameplay Layer
    subgraph "Real-time Layer (Stateful)"
        BattleServer[Team Battle Server\n(Game Logic, Physics, Room State)]
    end

    %% Modular Platform Services Layer
    subgraph "Modular Platform Services (Stateless)"
        AuthService[Auth Service\n(JWT, Identity)]
        UserProfile[User Profile / Inventory Service]
        
        subgraph "Requested Specific Modules"
            MasterConfig[Master Config Service\n(Single Source of Truth)]
            Leaderboard[Leaderboard Service\n(Ranking, Scoring)]
            LiveOps[LiveOps Engine Service\n(Events, Scheduling, Offers)]
        end
    end

    %% Data Layer
    subgraph "Data Persistence Layer"
        MongoPrimary[(MongoDB Cluster\nPersistent Storage: Profiles, History, Configs)]
        RedisCache[(Redis Cluster\nHot Data, Caching, Session, ephemeral state)]
    end

    %% ============ Connections ============

    %% Client Connectivity
    Client -->|HTTPS / WSS| LB
    LB --> Gateway
    
    Gateway -->|AUTH Check| AuthService
    Gateway -- REST/gRPC Routes --> UserProfile & MasterConfig & Leaderboard & LiveOps
    Gateway -- WebSocket Upgrade --> BattleServer

    %% Admin Flow for Config
    AdminUI -->|REST| MasterConfig

    %% --- Service to Data Layer (Direct) ---
    %% Redis Usage
    AuthService -->|Cache Session/Token| RedisCache
    BattleServer -->|Hot Room State/Match Data| RedisCache
    Leaderboard -->|Sorted Sets for fast ranking| RedisCache
    MasterConfig -->|Cache Config Data| RedisCache
    LiveOps -->|Cache Active Events| RedisCache

    %% MongoDB Usage
    UserProfile -->|Read/Write Profile Data| MongoPrimary
    MasterConfig -->|Read/Write Config Docs| MongoPrimary
    Leaderboard -->|Archive Historical Ranks| MongoPrimary
    LiveOps -->|Store Event Definitions| MongoPrimary
    BattleServer -->|Save Match Results (Async)| MongoPrimary

    %% --- NATS JetStream Event Flow (Asynchronous Gluing) ---
    
    %% Battle Events
    BattleServer -.- |Pub: match.ended, player.killed| NATS
    
    %% Reactors to Battle Events
    NATS -.- |Sub: match.ended (Update Score)| Leaderboard
    NATS -.- |Sub: match.ended (Give Rewards/XP)| UserProfile
    
    %% Config Updates Flow
    MasterConfig -.- |Pub: config.updated (v1.2 -> v1.3)| NATS
    NATS -.- |Sub: config.updated (Invalidate Local Cache)| BattleServer & LiveOps & Gateway

    %% LiveOps Trigger Flow
    LiveOps -.- |Pub: liveops.event.start| NATS
    NATS -.- |Sub: liveops.event.start (Enable In-game Feature)| BattleServer
```

-----

### Phân tích chi tiết các thành phần

#### 1\. Edge Layer (Lớp Biên)

  * **Load Balancer & API Gateway:** Điểm nhập duy nhất cho mọi yêu cầu từ Client. Nó chịu trách nhiệm định tuyến (routing), chấm dứt SSL, và quan trọng nhất là xác thực (Authentication check) với *Auth Service* trước khi cho phép request đi sâu vào hệ thống. Đối với game "Team Battle", Gateway cần hỗ trợ nâng cấp kết nối lên WebSocket (WSS) để duy trì kết nối thời gian thực với *Battle Server*.

#### 2\. Real-time Layer (Lớp Thời Gian Thực - Team Battle)

  * **Team Battle Server (Stateful):** Đây là nơi diễn ra trận đấu. Nó giữ trạng thái của phòng chơi (vị trí nhân vật, máu, kỹ năng).
      * **Giao tiếp:** Sử dụng WebSocket (qua Gateway) để nhận input từ người chơi và gửi về trạng thái game (game state snapshot) với độ trễ thấp nhất.
      * **Redis Usage:** Lưu trữ trạng thái "nóng" (ephemeral state) của trận đấu đang diễn ra để đảm bảo tốc độ truy xuất cực nhanh và có thể phục hồi nếu một instance server bị crash nhẹ.
      * **NATS Integration:** Khi trận đấu kết thúc hoặc có sự kiện quan trọng (ví dụ: "First Blood"), nó sẽ bắn một sự kiện (Publish Event) lên NATS JetStream (ví dụ: chủ đề `match.ended`) thay vì gọi trực tiếp các service khác. Điều này giúp Battle Server chỉ tập trung vào logic game.

#### 3\. NATS JetStream (Xương sống giao tiếp)

Đóng vai trò là hệ thống thần kinh trung ương, giúp phân tách (decouple) các microservices.

  * **Pub/Sub & Streaming:** Cho phép các service giao tiếp bất đồng bộ. JetStream đảm bảo tin nhắn không bị mất (durable stream) ngay cả khi một service đang offline tạm thời.
  * **Ví dụ luồng dữ liệu:** Khi `BattleServer` bắn sự kiện `match.ended` kèm kết quả trận đấu lên NATS.
      * `Leaderboard Service` nghe (subscribe) sự kiện này để cập nhật bảng xếp hạng.
      * `UserProfile Service` nghe để cộng kinh nghiệm (XP) và tiền vàng cho người chơi.
      * Việc thêm một service mới (ví dụ: Service thống kê lịch sử đấu) không làm ảnh hưởng đến Battle Server hiện tại, chỉ cần subscribe vào NATS là xong.

#### 4\. Modular Platform Services (Các Microservice theo module)

Đây là các service thường là "Stateless" và có thể scale ngang dễ dàng.

  * **A. Master Config System (Rất quan trọng):**

      * **Chức năng:** Là nguồn chân lý duy nhất (Single Source of Truth) cho mọi cấu hình trong game (thông số tướng, tỷ lệ rơi đồ, cài đặt hệ thống). Không được phép hardcode cấu hình trong code.
      * **Luồng hoạt động:** Admin thay đổi config trên Admin Portal -\> Master Config Service lưu vào **MongoDB** (để bền vững) -\> Đồng thời đẩy bản mới vào **Redis** (để đọc nhanh) -\> Bắn sự kiện `config.updated` lên **NATS**.
      * **Phản ứng:** Các service khác (Battle Server, Gateway) nghe sự kiện `config.updated` từ NATS để biết rằng cần phải xóa cache cục bộ và tải config mới từ Redis về.

  * **B. Leaderboard Service:**

      * **Chức năng:** Quản lý các bảng xếp hạng (theo mùa, theo tuần, theo sự kiện).
      * **Redis Usage:** Sử dụng cấu trúc dữ liệu **Sorted Sets (ZSET)** của Redis. Đây là công cụ hoàn hảo cho bảng xếp hạng thời gian thực vì nó hỗ trợ thêm điểm và lấy thứ hạng với tốc độ cực nhanh (O(log(N))).
      * **MongoDB Usage:** Định kỳ lưu snapshot của bảng xếp hạng vào MongoDB để lưu trữ lịch sử lâu dài.

  * **C. LiveOps Engine Service:**

      * **Chức năng:** Quản lý các sự kiện vận hành (ví dụ: Sự kiện X2 kinh nghiệm cuối tuần, bán gói quà đặc biệt trong 2 giờ).
      * **Hoạt động:** Nó dựa trên thời gian hoặc điều kiện để kích hoạt các trạng thái. Nó cũng sử dụng NATS để thông báo cho toàn hệ thống biết khi một sự kiện bắt đầu hoặc kết thúc (`liveops.event.start`).

#### 5\. Data Layer (Lớp Dữ Liệu)

  * **MongoDB (Persistent Storage):** Nơi lưu trữ dữ liệu gốc, cần sự bền vững và cấu trúc linh hoạt (Schema-less). Phù hợp cho Profile người chơi (Inventory thường phức tạp), lịch sử trận đấu, và các file cấu hình JSON lớn.
  * **Redis (Caching & Hot State):** Lớp đệm tốc độ cao. Bất cứ dữ liệu nào cần truy cập trong vài mili giây (ms) đều phải nằm ở đây: Session người dùng, Config game, Bảng xếp hạng nóng, Trạng thái trận đấu đang diễn ra.

### Tổng kết

Kiến trúc này đảm bảo:

1.  **Hiệu năng cao (Performance):** Nhờ Redis và NATS cho các tác vụ thời gian thực.
2.  **Khả năng mở rộng (Scalability):** Các microservice có thể scale độc lập. Battle Server có thể thêm instance khi lượng người chơi tăng.
3.  **Độ tin cậy (Reliability):** NATS JetStream đảm bảo không mất mát dữ liệu sự kiện quan trọng. MongoDB đảm bảo an toàn dữ liệu người dùng.
4.  **Linh hoạt (Flexibility):** Kiến trúc modular (Leaderboard, LiveOps tách riêng) giúp dễ dàng thêm tính năng mới mà không ảnh hưởng hệ thống cũ.