# Ôn tập Prisma ORM, WebSocket và Soketi, Xây dựng ứng dụng Chat

## 1. Cheat Sheet các method CRUD của Prisma

- Prisma Client cung cấp các method để thao tác với database thông qua tên model viết thường theo kiểu camelCase
- Ví dụ model tên là Product thì truy cập bằng prisma.product
- Lấy dữ liệu
  - findMany - Lấy danh sách bản ghi
    - Lấy tất cả bản ghi không có điều kiện
      ```javascript
      const users = await prisma.user.findMany();
      ```
    - Lấy bản ghi có điều kiện lọc
      ```javascript
      const users = await prisma.user.findMany({
        where: { email: { contains: "example" } },
      });
      ```
    - Hỗ trợ phân trang với skip và take
      ```javascript
      const users = await prisma.user.findMany({
        skip: 0,
        take: 10,
      });
      ```
    - Hỗ trợ sắp xếp với orderBy
      ```javascript
      const users = await prisma.user.findMany({
        orderBy: { createdAt: "desc" },
      });
      ```
    - Hỗ trợ chọn trường trả về với select
      ```javascript
      const users = await prisma.user.findMany({
        select: { id: true, name: true },
      });
      ```
    - Hỗ trợ lấy kèm quan hệ với include
      ```javascript
      const users = await prisma.user.findMany({
        include: { posts: true },
      });
      ```
  - findUnique - Lấy một bản ghi duy nhất theo trường unique (id, email,...)
    ```javascript
    const user = await prisma.user.findUnique({
      where: { id: 1 },
    });
    ```
  - findUniqueOrThrow - Giống findUnique nhưng tự động ném lỗi nếu không tìm thấy bản ghi
    - Hữu ích khi cần đảm bảo bản ghi phải tồn tại, không cần tự viết logic kiểm tra null
    ```javascript
    const user = await prisma.user.findUniqueOrThrow({
      where: { id: 1 },
    });
    ```
  - findFirst - Lấy ban ghi đầu tiên khớp điều kiện
    ```javascript
    const user = await prisma.user.findFirst({
      where: { name: "Alice" },
    });
    ```
  - count - Đếm số lượng bản ghi
    ```javascript
    const total = await prisma.user.count({
      where: { isActive: true },
    });
    ```
- Tạo dữ liệu
  - create - Tạo một bản ghi mới
    ```javascript
    const user = await prisma.user.create({
      data: {
        name: "Alice",
        email: "alice@example.com",
      },
    });
    ```
  - createMany - Tạo nhiều bản ghi cùng lúc

    ```javascript
    const result = await prisma.user.createMany({
      data: [
        { name: "Alice", email: "alice@example.com" },
        { name: "Bob", email: "bob@example.com" },
      ],
    });
    ```

    - Trả về số lượng bản ghi được tạo thay vì trả về danh sách bản ghi

- Cập nhật dữ liệu
  - update - Cập nhật một bản ghi theo điều kiện unique
    ```javascript
    const user = await prisma.user.update({
      where: { id: 1 },
      data: { name: "Alice Updated" },
    });
    ```
  - updateMany - Cập nhật nhiều bản ghi khớp điều kiện

    ```javascript
    const result = await prisma.user.updateMany({
      where: { isActive: false },
      data: { isActive: true },
    });
    ```

    - Trả về số lượng bản ghi được cập nhật

  - upsert - Tạo mới nếu chưa tồn tại, cập nhật nếu đã tồn tại
    ```javascript
    const user = await prisma.user.upsert({
      where: { email: "alice@example.com" },
      update: { name: "Alice Updated" },
      create: { name: "Alice", email: "alice@example.com" },
    });
    ```

- Xóa dữ liệu
  - delete - Xóa một bản ghi theo điều kiện unique
    ```javascript
    const user = await prisma.user.delete({
      where: { id: 1 },
    });
    ```
  - deleteMany - Xóa nhiều bản ghi khớp điều kiện

    ```javascript
    const result = await prisma.user.deleteMany({
      where: { isActive: false },
    });
    ```

    - Gọi không truyền where sẽ xóa toàn bộ bản ghi trong bảng

- Các lưu ý quan trọng
  - Khi truyền giá trị từ request params hoặc query string vào Prisma, cần ép kiểu cho đúng với kiểu dữ liệu trong database
  - Ví dụ trường id kiểu Int trong database thì phải ép parseInt trước khi truyền vào where, nếu truyền chuỗi Prisma sẽ báo lỗi
  - Prisma hỗ trợ quan hệ dữ liệu (relation) cho phép tạo bản ghi cha và con cùng lúc thông qua nested writes
  - Tài liệu tham khảo chính thức tại https://www.prisma.io/docs/reference/api-reference/prisma-client-reference

## 2. Hướng dẫn cài đặt NVM (Node Version Manager)

- NVM là gì
  - NVM viết tắt của Node Version Manager
  - Là công cụ giúp quản lý và chuyển đổi giữa nhiều phiên bản Node.js trên cùng một máy
  - Hữu ích khi các dự án khác nhau yêu cầu các phiên bản Node.js khác nhau
  - Ví dụ Soketi yêu cầu Node 14 hoặc 18, trong khi dự án khác có thể cần Node 20
- Cài đặt NVM trên macOS và Linux
  - Mở terminal và chạy lệnh cài đặt từ GitHub chính thức
    ```bash
      curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
    ```
  - Sau khi cài xong, đóng terminal và mở lại để NVM có hiệu lực
  - Kiểm tra cài đặt thành công
    ```bash
      nvm --version
    ```
- Cài đặt NVM trên Windows
  - NVM phiên bản gốc chỉ hỗ trợ macOS và Linux
  - Trên Windows sử dụng nvm-windows, tải tại https://github.com/coreybutler/nvm-windows/releases
  - Tải file nvm-setup.exe từ trang releases và chạy cài đặt bình thường
  - Sau khi cài xong, mở Command Prompt hoặc PowerShell và kiểm tra
    ```bash
      nvm version
    ```
- Các lệnh NVM thường dùng
  - Cài đặt một phiên bản Node cụ thể
    ```bash
      nvm install 18
    ```
  - Chuyển sang phiên bản Node đã cài
    ```bash
      nvm use 18
    ```
  - Xem phiên bản Node đang dùng
    ```bash
      node -v
    ```
  - Xem danh sách các phiên bản Node đã cài
    ```bash
      nvm ls
    ```
  - Đặt phiên bản mặc định
    ```bash
      nvm alias default 18
    ```
- Lưu ý quan trọng
  - Khi chuyển phiên bản Node, các package cài global ở phiên bản cũ sẽ không có ở phiên bản mới
  - Cần cài lại các package global sau khi chuyển phiên bản
  - Ví dụ sau khi chuyển sang Node 18, nếu cần dùng Soketi phải cài lại
    ```bash
      npm install -g @soketi/soketi
    ```

## 3. Hướng dẫn setup Soketi và chạy trên Windows

- Soketi là gì
  - Là một WebSocket server mã nguồn mở, miễn phí, tương thích với giao thức Pusher
  - Thay thế dịch vụ Pusher trả phí, cho phép tự host trên máy chủ riêng
  - Với cấu hình tối thiểu 1 CPU và 1 GB RAM có thể gánh được tới 200.000 kết nối đồng thời
  - Độ trễ chỉ khoảng 6ms nên rất phù hợp cho ứng dụng thời gian thực
  - Trang chủ tại https://soketi.app
- Cài đặt Soketi
  - Cài global bằng npm
    ```bash
      npm install -g @soketi/soketi
    ```
  - Soketi yêu cầu Node.js phiên bản 14 hoặc 18, sử dụng NVM để chuyển đúng phiên bản trước khi cài
- Tạo file cấu hình Soketi
  - Tạo file JSON chứa thông tin xác thực cho ứng dụng
  - Ví dụ tạo file tên soketi_config.json với nội dung sau
    ```json
    {
      "appManager.array.apps": [
        {
          "id": "app-1",
          "key": "app-key",
          "secret": "app-secret"
        }
      ]
    }
    ```
  - Trường id là định danh duy nhất cho ứng dụng
  - Trường key là khóa công khai, dùng ở phía frontend để kết nối
  - Trường secret là khóa bí mật, chỉ dùng ở phía backend để trigger sự kiện, không được để lộ ra frontend
  - Có thể tạo file bằng VS Code, Notepad hoặc bất kỳ trình soạn thảo nào
  - Các giá trị key và secret trong thực tế nên dùng chuỗi ngẫu nhiên để bảo mật
- Tạo file cấu hình bằng dòng lệnh (tùy chọn)
  - Mở Git Bash hoặc terminal
  - Dùng trình soạn thảo Vim để tạo file
    ```bash
      vi soketi_config.json
    ```
  - Bấm phím i để vào chế độ insert (chèn nội dung)
  - Dán hoặc gõ nội dung JSON vào
  - Bấm ESC để thoát chế độ insert
  - Dùng phím mũi tên để di chuyển con trỏ
  - Bấm dd để xóa một dòng
  - Bấm Shift rồi gõ hai chấm, sau đó gõ x và Enter để lưu và thoát
- Chạy Soketi với file cấu hình
  - Sử dụng lệnh sau, truyền đường dẫn tuyệt đối đến file cấu hình
    ```bash
      soketi start --config=/đường/dẫn/tuyệt/đối/soketi_config.json
    ```
  - Trên Windows với Git Bash, đường dẫn tuyệt đối ví dụ
    ```bash
      soketi start --config=C:/Users/TenNguoiDung/project/soketi_config.json
    ```
  - Trên Windows với Command Prompt hoặc PowerShell
    ```bash
      soketi start --config=C:\Users\TenNguoiDung\project\soketi_config.json
    ```
  - Sau khi chạy thành công, máy chủ WebSocket sẵn sàng ở cổng mặc định là 6001
  - Mặc định Soketi chạy ở cổng 6001 cho WebSocket và 6002 cho các kết nối HTTP
- Kết nối Frontend với Soketi bằng Pusher JS
  - Cài thư viện pusher-js vào dự án frontend
    ```bash
      npm install pusher-js
    ```
  - Tạo file cấu hình kết nối, ví dụ socketClient.js

    ```javascript
    import Pusher from "pusher-js";

    const socketClient = new Pusher("app-key", {
      cluster: "mt1",
      wsHost: "127.0.0.1",
      wsPort: 6001,
      forceTLS: false,
      disableStats: true,
    });

    export default socketClient;
    ```

  - Giá trị app-key phải trùng với key trong file cấu hình Soketi
  - cluster có thể điền giá trị bất kỳ vì Soketi không dùng đến, nhưng là trường bắt buộc của Pusher JS
  - wsHost trỏ đến địa chỉ máy chủ Soketi đang chạy
  - forceTLS đặt false khi chạy ở local vì dùng HTTP, đặt true khi deploy lên server có HTTPS

- Kết nối Backend với Soketi bằng Pusher SDK
  - Cài thư viện pusher vào dự án backend
    ```bash
      npm install pusher
    ```
  - Tạo file cấu hình, ví dụ libPusher.js

    ```javascript
    const Pusher = require("pusher");

    const pusher = new Pusher({
      appId: process.env.SOCKET_APP_ID,
      key: process.env.SOCKET_APP_KEY,
      secret: process.env.SOCKET_APP_SECRET,
      cluster: "mt1",
      host: "127.0.0.1",
      port: 6002,
      useTLS: false,
    });

    module.exports = pusher;
    ```

  - Lưu appId, key và secret vào file .env để bảo mật, không hardcode trong source code
  - secret chỉ dùng ở backend, tuyệt đối không đưa ra frontend

- Subscribe và lắng nghe sự kiện ở Frontend
  - Subscribe một kênh
    ```javascript
    const channel = socketClient.subscribe("zoom-1");
    ```
  - Lắng nghe sự kiện trên kênh đó
    ```javascript
    channel.bind("message", (data) => {
      console.log(data);
    });
    ```
  - Hủy theo dõi khi rời khỏi man hình hoặc chuyển kênh
    ```javascript
    socketClient.unsubscribe("zoom-1");
    ```
- Trigger sự kiện từ Backend
  - Gửi dữ liệu vào một kênh cụ thể với tên sự kiện
    ```javascript
    pusher.trigger("zoom-1", "message", {
      action: "created",
      data: messageData,
    });
    ```
  - Tất cả các client đang subscribe kênh đó và bind đúng tên sự kiện sẽ nhận được dữ liệu ngay lập tức
