# Bài 4 : Sự cố xung đột phiên đăng nhập trên trình duyệt
## Phần 1: Phân tích
### 1. So sánh Incognito Mode và Dedicated Profile
|Tiêu chí|Incognito Mode|Dedicated Profile|
|---|---|---|
|Lưu trữ dữ liệu|Tạm thời trong phiên làm việc|Lưu lâu dài trên ổ cứng|
|Cookies|Xóa khi đóng cửa sổ|Được giữ lại|
|Lịch sử duyệt web|Không lưu sau khi đóng|Lưu riêng cho từng Profile|
|Mật khẩu đã lưu|Không ghi nhớ mới|Có thể lưu và đồng bộ|
|Bookmark|Không lưu lâu dài|Lưu vĩnh viễn|
|Đăng nhập tài khoản|Phải đăng nhập lại mỗi lần|Duy trì phiên đăng nhập|
|Tách biệt dữ liệu|Chỉ trong phiên hiện tại|Hoàn toàn độc lập|
|Phù hợp sử dụng|Máy lạ, máy công cộng|Môi trường làm việc thường xuyên|

**Minh họa trạng thái lưu trữ**

Incognito Mode
```Text
RAM
│
├── Cookies tạm
├── Session tạm
├── Cache tạm
└── Đóng cửa sổ ⇒ Xóa toàn bộ
```
Dedicated Profile
```Text
Disk Storage
│
├── Profile Personal
│ ├── History
│ ├── Cookies
│ ├── Passwords
│ └── Bookmarks
│
├── Profile Work
│ ├── History
│ ├── Work Account
│ └── Company Portal
│
└── Profile Sandbox
├── Test Account
├── Dev Tools
└── Temporary Resources
```
### 2. Mô hình 3 Profile đề xuất
Để triệt tiêu hoàn toàn hiện tượng Session Conflict:
```text
Chrome / Edge
│
├── Personal
│   ├── Gmail cá nhân
│   ├── Facebook
│   ├── Banking
│   └── Password Manager
│
├── Work
│   ├── GreenLog Admin
│   ├── Company Portal
│   ├── Outlook
│   └── Teams
│
└── Sandbox
    ├── Test User
    ├── Demo Data
    ├── QA Testing
    └── Experimental Login
```
**Lợi ích**
- Không bị văng tài khoản khi đăng nhập nhiều môi trường.
- Cookie và Session tách biệt hoàn toàn.
- Mật khẩu cá nhân không xuất hiện ở môi trường làm việc.
- An toàn khi trình chiếu hoặc chia sẻ màn hình.
## Phần 2:Triển khai
### 1. Quy trình tạo 3 Profile trên Google Chrome
**Bước 1: Tạo Profile Personal**
1. Mở Google Chrome.
2. Nhấn biểu tượng Profile ở góc trên bên phải.
3. Chọn Add Chrome Profile hoặc Add.
4. Đặt tên Personal.
5. Chọn màu sắc và biểu tượng riêng.

**Bước 2: Tạo Profile Work**
1. Tiếp tục chọn Add Profile.
2. Đặt tên Work.
3. Đăng nhập tài khoản Google phục vụ công việc nếu được phép.
4. Thiết lập Bookmark dành riêng cho GreenLog.

**Bước 3: Tạo Profile Sandbox**
1. Tiếp tục chọn Add Profile.
2. Đặt tên Sandbox.
3. Sử dụng tài khoản kiểm thử riêng.
4. Không sử dụng dữ liệu hoặc tài khoản quản trị thật trong môi trường thử nghiệm.

**Kết quả:** Có 3 Profile độc lập, có thể mở đồng thời mà không cần liên tục đăng xuất và đăng nhập lại.
### 2. Xây dựng cấu trúc cây thư mục Bookmark
Mục tiêu là sắp xếp các trang web theo nhóm chức năng để dễ dàng tìm kiếm và truy cập.

Cấu trúc đề xuất:
```text
BOOKMARKS
│
├── 01_PERSONAL
│   ├── Gmail
│   ├── YouTube
│   └── Facebook
│
├── 02_WORK_GREENLOG
│   ├── GitHub
│   ├── Canvas LMS
│   ├── GreenLog Portal
│   ├── Google Drive
│   └── Project Management
│
└── 03_SANDBOX
    ├── Test Environment
    ├── Demo Website
    └── API Testing
```
**Cách tạo thư mục Bookmark:**
1. Mở Profile cần thiết.
2. Nhấn Ctrl + Shift + O để mở Bookmark Manager.
3. Nhấn dấu ba chấm.
4. Chọn Add new folder.
5. Nhập tên thư mục.
6. Tạo các thư mục con và thêm website tương ứng.
### 3. Thiết lập Cloud Sync để bảo vệ Bookmark
Cloud Sync giúp đồng bộ Bookmark và các dữ liệu được chọn giữa nhiều thiết bị.

**Quy trình thực hiện:**
1. Mở Profile Work.
2. Đăng nhập tài khoản Google phù hợp.
3. Chọn biểu tượng Profile.
4. Bật tính năng Turn on sync hoặc vào phần đồng bộ hóa trong Settings.
5. Mở mục You and Google → Sync and Google services → Manage what you sync.
6. Chọn đồng bộ Bookmarks.
7. Kiểm tra trạng thái đồng bộ.

**Cơ chế hoạt động:**
- Máy tính thứ nhất lưu Bookmark vào Profile Work.
- Chrome đồng bộ Bookmark với tài khoản Google.
- Khi đăng nhập cùng tài khoản trên máy tính thứ hai và bật Sync, Bookmark được đồng bộ xuống thiết bị mới.
## Phần 3: Kiểm thử
**Tình huống: Làm việc trên máy tính công cộng**

Khi sử dụng máy tính tại thư viện, phòng máy hoặc quán Internet, quản trị viên nên sử dụng Guest Profile (Chế độ khách) để hạn chế lưu lại thông tin cá nhân.
### 1. Quy trình sử dụng Guest Profile
1. Mở Google Chrome.
2. Nhấn biểu tượng Profile.
3. Chọn Guest (Khách).
4. Truy cập website cần sử dụng.
5. Nếu cần đăng nhập, chỉ sử dụng trong phiên làm việc cần thiết.
6. Sau khi hoàn thành, đóng toàn bộ cửa sổ Guest.
### 2. Các quy tắc bảo mật
|Quy tắc|	Mục đích|
|---|---|
|Không đăng nhập tài khoản cá nhân trên Profile chính của máy công cộng	|Tránh lưu thông tin vào hồ sơ người khác|
|Không bật Cloud Sync|	Tránh đồng bộ dữ liệu cá nhân lên máy công cộng|
|Không lưu mật khẩu	|Hạn chế lộ thông tin đăng nhập|
|Không tải hoặc lưu tài liệu nhạy cảm	|Tránh để lại dữ liệu trên máy|
|Đóng toàn bộ cửa sổ Guest|	Xóa dữ liệu phiên khách|
|Đăng xuất khỏi các dịch vụ quan trọng|	Hạn chế nguy cơ còn phiên đăng nhập|
