# Ôn tập Prisma ORM, WebSocket và Soketi, Xây dựng ứng dụng Chat

## 1. Ôn tập Prisma ORM

- Khởi động lại dự án sau khi clone từ GitHub
  - Khi clone dự án về lần đầu, cần chạy các bước sau trước khi khởi động
  - npm install để cài lại toàn bộ dependency
  - Thư mục generated của Prisma bị bỏ qua bởi git, nên sẽ không có sẵn sau khi clone
    - Nếu chạy npm run dev mà gặp lỗi không tìm thấy module `@prisma/client`, chạy lệnh sau
      ```bash
        npx prisma generate
      ```
    - Lệnh này tái tạo lại thư mục generated để dự án chạy được
  - File .env cũng bị git bỏ qua để bảo vệ thông tin bí mật
    - Dự án thường có file .env.example để làm mẫu
    - Copy file đó ra và đổi tên thành .env, rồi điền đúng các thông số
  - Nếu database chưa tồn tại, chạy lệnh sau để tạo lại toàn bộ cấu trúc bảng
    ```bash
      npx prisma migrate dev
    ```

- Quy trình ba bước khi thay đổi cấu trúc database
  - Đây là quy trình bắt buộc mỗi khi cần thêm, sửa hoặc xóa bảng hay trường trong database
  - Bước 1 - Viết schema trong file prisma/schema.prisma
    - Thêm hoặc chỉnh sửa model theo nhu cầu
    - Ví dụ thêm bảng Product
      ```prisma
        model Product {
        id Int @id @default(autoincrement())
        name String
        price Float
        description String @db.Text
        content String @db.Text
        quantity Int
        createdAt DateTime @default(now())
        }
      ```
  - Bước 2 - Chạy migration để đồng bộ database

    ```bash
      npx prisma migrate dev --name create_product_table
    ```

    - Prisma tự tạo file migration trong thư mục prisma/migrations và áp dụng vào database thật

  - Bước 3 - Chạy generate để cập nhật Prisma Client

    ```bash
      npx prisma generate
    ```

    - Bước này cần thiết để code có thể sử dụng model mới vừa tạo

  - Sau ba bước này mới bắt đầu viết code xử lý API

- Sử dụng Prisma Client trong code
  - Truy cập model thông qua tên model viết thường theo kiểu camelCase
    - Ví dụ model tên là Product thì truy cập bằng prisma.product
  - Lấy tất cả bản ghi
    ```javascript
    const products = await prisma.product.findMany();
    ```
  - Truy vấn theo điều kiện
    ```javascript
    const product = await prisma.product.findUniqueOrThrow({
      where: { id: parseInt(req.params.id) },
    });
    ```
  - findUniqueOrThrow sẽ tự động văng lỗi nếu không tìm thấy bản ghi, giúp xử lý lỗi tập trung
  - Thay vì viết query SQL thủ công, Prisma giúp giảm độ phức tạp và hạn chế lỗ hổng SQL injection

## 2. WebSocket - Khái niệm và các phương pháp kết nối thời gian thực

- Vấn đề cần giải quyết
  - Các ứng dụng cần cập nhật dữ liệu ngay lập tức như chat, thông báo, game
  - Giao thức HTTP thông thường yêu cầu client gửi yêu cầu trước mới nhận được phản hồi
  - Kết nối HTTP kết thúc ngay sau khi nhận xong phản hồi, server không thể chủ động gửi dữ liệu về client

- Short Polling
  - Cách hoạt động
    - Frontend dùng setInterval để định kỳ gọi API lên server, ví dụ mỗi 3 giây một lần
    - Server trả về danh sách dữ liệu mới nhất, frontend so sánh và cập nhật giao diện nếu có thay đổi
  - Nhược điểm
    - Độ trễ cao, dữ liệu trễ theo chu kỳ polling, không phải thời gian thực thực sự
    - Tăng tải server do liên tục nhận request dù không có dữ liệu mới
    - Không phù hợp với ứng dụng đòi hỏi độ trễ thấp

- Long Polling
  - Cách hoạt động
    - Client gửi một request lên server, server không phản hồi ngay mà giữ request lại
    - Server chạy vòng lặp kiểm tra database, ví dụ mỗi 1 giây một lần, trong tối đa 30 giây
    - Nếu có dữ liệu mới, server trả về ngay lập tức
    - Nếu hết thời gian chờ mà không có gì mới, server mới trả về rỗng và client gửi request tiếp theo
  - Ưu điểm so với Short Polling
    - Giảm số lượng request, tạo cảm giác gần thời gian thực hơn
  - Nhược điểm
    - Vẫn tốn tài nguyên server do phải giữ nhiều kết nối đồng thời
    - Facebook Messenger đã từng sử dụng kỹ thuật này trước khi WebSocket phổ biến
  - Hiện nay cả Short Polling lẫn Long Polling gần như không còn được dùng

- WebSocket
  - Khái niệm
    - WebSocket là giao thức được nâng cấp từ HTTP, tạo ra kết nối hai chiều và duy trì liên tục
    - Sau khi kết nối được thiết lập, cả client và server đều có thể chủ động gửi dữ liệu cho nhau bất kỳ lúc nào
    - Kết nối không đóng cho đến khi một bên chủ động ngắt
  - So sánh với HTTP
    - HTTP là giao tiếp một chiều, client gửi lên thì server mới phản hồi, sau đó kết nối kết thúc
    - WebSocket giống như một cuộc điện thoại, hai bên liên tục liên lạc qua một kênh duy nhất
  - Cơ chế hoạt động
    - Client gửi yêu cầu nâng cấp kết nối (handshake) lên server
    - Server chấp nhận và kết nối được nâng cấp thành giao thức WebSocket
    - Sau đó cả hai bên có thể trao đổi dữ liệu tự do qua kết nối đang mở
    - Cơ chế ping-pong được dùng để kiểm tra kết nối còn sống hay không
      - Client gửi ping, server phản hồi pong
      - Nếu không thấy pong, client biết kết nối đã chết và cố gắng kết nối lại
  - Kiến trúc ứng dụng với WebSocket
    - Frontend kết nối đến WebSocket server ngay khi tải trang
    - Các thao tác thông thường như gửi tin nhắn vẫn đi qua HTTP API vào backend
    - Backend sau khi xử lý xong lại gửi sự kiện sang WebSocket server
    - WebSocket server phân phối sự kiện đến tất cả client đang lắng nghe trên cùng kênh
  - Mô hình Publish và Subscribe
    - WebSocket server quản lý các kênh (channel)
    - Client đăng ký lắng nghe một kênh (subscribe)
    - Backend gửi sự kiện vào kênh (publish hay trigger)
    - Tất cả client đang subscribe kênh đó đều nhận được sự kiện ngay lập tức

## 3. Soketi - Tự triển khai WebSocket server

- Lý do chọn Soketi
  - socket.io là thư viện phổ biến nhưng tốn nhiều công thiết lập và tự cấu hình
  - Pusher là dịch vụ WebSocket thương mại phổ biến, dễ dùng nhưng tốn tiền
    - Gói miễn phí giới hạn 100 kết nối đồng thời và 200.000 message mỗi ngày
    - Muốn nâng lên 500 kết nối đồng thời phải trả khoảng 50 USD mỗi tháng
  - Soketi là WebSocket server mã nguồn mở, tương thích hoàn toàn với giao thức Pusher
    - Cài lên VPS của mình, chạy cùng server web, không mất thêm phí dịch vụ
    - Một máy chủ cấu hình tối thiểu (1 CPU, 1 GB RAM) có thể xử lý hàng trăm nghìn kết nối
    - Độ trễ chỉ vài millisecond

- Cài đặt và khởi động Soketi
  - Yêu cầu Node.js phiên bản 14 hoặc 18, dùng NVM để quản lý nhiều phiên bản Node.js
    - Cài NVM từ kho GitHub chính thức tại github.com/nvm-sh/nvm
    - Sau khi cài NVM, cài Node 18 bằng lệnh sau
      ```bash
        nvm install 18
      ```
  - Cài Soketi CLI toàn cục
    ```bash
      npm install -g @soketi/soketi
    ```
  - Tạo file cấu hình socketi-config.json

    ```json
    {
      "debug": true,
      "port": 6002,
      "appManager.driver": "array",
      "appManager.array.apps": [
        {
          "id": "app-1",
          "key": "app-key-1",
          "secret": "app-secret-1",
          "webhooks": []
        }
      ]
    }
    ```

    - Lưu ý xóa phần webhooks hoặc đảm bảo file JSON hợp lệ, không có dấu phẩy thừa

  - Khởi động Soketi với file cấu hình
    ```bash
      soketi start --config=socketi-config.json
    ```
  - Một ứng dụng (app) tương ứng với một trang web, có thể định nghĩa nhiều app trong cùng một máy chủ
  - Mỗi app có ba thông số quan trọng
    - id dùng để nhận dạng app
    - key dùng ở phía frontend để kết nối
    - secret dùng ở phía backend để gửi sự kiện, tuyệt đối không được để lộ ra client

- Kết nối từ Frontend với pusher-js
  - Cài thư viện
    ```bash
      npm install pusher-js
    ```
  - Khởi tạo Pusher client trong file riêng để tái sử dụng

    ```javascript
    import Pusher from "pusher-js";

    const socketClient = new Pusher("app-key-1", {
      cluster: "mt1",
      wsHost: "127.0.0.1",
      wsPort: 6002,
      forceTLS: false,
      enabledTransports: ["ws", "wss"],
    });

    export default socketClient;
    ```

  - Cluster có thể điền bất kỳ giá trị nào khi dùng Soketi vì không áp dụng clustering thực sự
  - Sau khi khởi tạo, kết nối WebSocket sẽ hiển thị trong tab Network của DevTools với trạng thái pending, nghĩa là kết nối đang được giữ sống

- Subscribe channel và lắng nghe sự kiện từ Frontend

  ```javascript
  const channel = socketClient.subscribe("conversation-5");

  channel.bind("message", (data) => {
    console.log(data);
  });
  ```

  - Khi không dùng nữa cần unsubscribe để giải phóng tài nguyên
    ```javascript
    channel.unsubscribe();
    ```

- Gửi sự kiện từ Backend với thư viện Pusher server
  - Cài thư viện vào dự án backend
    ```bash
      npm install pusher
    ```
  - Khởi tạo Pusher instance trong file riêng ở backend

    ```javascript
    const Pusher = require("pusher");

    const pusher = new Pusher({
      appId: process.env.SOCKET_APP_ID,
      key: process.env.SOCKET_APP_KEY,
      secret: process.env.SOCKET_APP_SECRET,
      host: "127.0.0.1",
      port: "6002",
      useTLS: false,
    });

    module.exports = pusher;
    ```

  - Gửi sự kiện vào channel từ backend
    ```javascript
    await pusher.trigger("conversation-5", "message", {
      action: "created",
      payload: message,
    });
    ```
  - Luồng hoàn chỉnh
    - Frontend gửi HTTP POST lên backend để lưu tin nhắn vào database
    - Backend lưu xong, gọi pusher.trigger để publish sự kiện vào channel tương ứng
    - Soketi server nhận sự kiện và phân phối đến tất cả frontend đang subscribe channel đó
    - Frontend nhận được sự kiện trong callback của channel.bind và cập nhật giao diện ngay lập tức

## 4. Xây dựng ứng dụng Chat

- Thiết kế database
  - Bảng User - đại diện người dùng, đã có sẵn từ phần authentication
  - Bảng Conversation - lưu các cuộc hội thoại
    - id, name (có thể null, dùng cho chat nhóm), type (DM hoặc group)
    - name có thể để null vì chat trực tiếp không cần tên nhóm
  - Bảng ConversationUser - bảng trung gian thể hiện quan hệ nhiều nhiều giữa Conversation và User
    - conversationId, userId
    - Một cuộc hội thoại có nhiều thành viên, một người có thể tham gia nhiều cuộc hội thoại
  - Bảng Message - lưu tin nhắn
    - id, content (kiểu Text, không dùng String vì tin nhắn có thể dài), type (text, image, like,...), conversationId, userId
    - Dùng varchar thông thường cho type để dễ mở rộng sau này
  - Schema Prisma tương ứng

    ```prisma
      model Conversation {
      id Int @id @default(autoincrement())
      name String?
      type String @default("DM")
      conversationUser ConversationUser[]
      message Message[]
      }

      model ConversationUser {
      id Int @id @default(autoincrement())
      conversationId Int
      userId Int
      conversation Conversation @relation(fields: [conversationId], references: [id])
      user User @relation(fields: [userId], references: [id])
      }

      model Message {
      id Int @id @default(autoincrement())
      content String @db.Text
      type String @default("text")
      conversationId Int
      userId Int
      conversation Conversation @relation(fields: [conversationId], references: [id])
      user User @relation(fields: [userId], references: [id])
      }
    ```

- Bảo mật conversation ID
  - Dùng ID tự tăng khiến người dùng có thể dò ra conversation của người khác bằng cách thay đổi số trên URL
  - Giải pháp là thêm trường UUID vào bảng Conversation và dùng UUID trên URL thay cho ID số
    ```javascript
    const { randomUUID } = require("crypto");
    const uuid = randomUUID();
    ```
  - UUID là chuỗi ngẫu nhiên như `550e8400-e29b-41d4-a716-446655440000`, không thể dò theo thứ tự
  - UUID được tạo theo thuật toán đảm bảo không trùng, không cần kiểm tra trùng lặp khi insert

- Luồng xử lý cuộc hội thoại mới
  - Frontend hiển thị danh sách người dùng, người dùng bấm vào một người để bắt đầu chat
  - Điều hướng sang trang /new-chat với query string chứa userId của người được chọn
    ```jsx
    <Link to={`/new-chat?userId=${user.id}`}>{user.name}</Link>
    ```
  - Trang new-chat chưa tạo conversation ngay để tránh tạo dữ liệu thừa trong database
  - Chỉ khi người dùng gửi tin nhắn đầu tiên mới thực hiện
    - Gọi API tạo conversation mới với type là DM và danh sách userIds gồm cả mình và người kia
    - Gọi tiếp API tạo message đầu tiên vào conversation vừa tạo
    - Điều hướng sang trang /conversation/:id để tiếp tục chat
  - Cần kiểm tra trước khi tạo xem hai người này đã có conversation DM chưa
    - Nếu đã có thì trả về conversation cũ thay vì tạo mới, tránh duplicate

- API backend cần thiết
  - GET /users - lấy danh sách người dùng để hiển thị cho người chọn chat
  - POST /conversation - tạo conversation mới, nhận type và userIds trong body
    - Tự động thêm cả người đang đăng nhập vào conversation
    - Insert các thành viên vào bảng ConversationUser
  - POST /conversation/:id/message - tạo tin nhắn trong conversation
    - Sau khi lưu vào database, trigger sự kiện WebSocket để thông báo cho các client đang subscribe
  - GET /conversation/:id/message - lấy danh sách tin nhắn của một conversation
    ```javascript
    const messages = await prisma.message.findMany({
      where: { conversationId: parseInt(req.params.id) },
    });
    ```

- Xử lý real-time trong Frontend
  - Khi vào trang conversation, subscribe vào channel tương ứng bằng conversationId

    ```javascript
    useEffect(() => {
      const channel = socketClient.subscribe(`conversation-${conversationId}`);

      channel.bind("message", (data) => {
        if (data.action === "created") {
          setMessages((prev) => [...prev, data.payload]);
        }
        if (data.action === "updated") {
          setMessages((prev) =>
            prev.map((m) => (m.id === data.payload.id ? data.payload : m)),
          );
        }
        if (data.action === "deleted") {
          setMessages((prev) => prev.filter((m) => m.id !== data.payload.id));
        }
      });

      return () => {
        channel.unsubscribe();
      };
    }, [conversationId]);
    ```

  - Dùng action field trong payload để phân biệt loại sự kiện, tránh phải tạo nhiều channel khác nhau
  - Cleanup function unsubscribe cũ khi component unmount hoặc khi conversationId thay đổi

- Phân biệt tin nhắn của mình và người khác
  - Mỗi message đều có userId của người gửi
  - So sánh userId đó với userId của người đang đăng nhập hiện tại
  - Nếu trùng thì hiển thị tin nhắn bên phải, ngược lại hiển thị bên trái

- Client events - typing indicator
  - WebSocket server hỗ trợ cơ chế phát sự kiện trực tiếp giữa các client trong cùng channel mà không cần đi qua backend
  - Phù hợp để làm tính năng hiển thị trạng thái đang gõ
  - Khi người dùng bắt đầu gõ, client phát một sự kiện, các client khác trong channel nhận được và hiển thị thông báo đang gõ
  - Sự kiện này vẫn đi qua Soketi server nhưng không đi qua backend API, giảm tải đáng kể
