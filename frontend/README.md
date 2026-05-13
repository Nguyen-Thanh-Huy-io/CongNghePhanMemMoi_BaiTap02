# Edit Profile - Redux Hooks + Axios + UI

Tài liệu này hướng dẫn thiết kế và triển khai chức năng **Edit Profile** trong frontend.
Nội dung bao gồm:
- Cách dùng axios để gọi API
- Cách dùng Redux hook để quản lý trạng thái
- Thiết kế giao diện UI cho trang Edit Profile

## 1. Tổng quan chức năng

Chức năng Edit Profile cho phép người dùng:
- Xem thông tin profile hiện tại
- Chỉnh sửa fullName, phone, address, bio
- Upload avatar mới
- Lưu thay đổi bằng API

Frontend sử dụng:
- React + TypeScript
- Redux Toolkit với async thunks
- TailwindCSS cho thiết kế UI
- Axios cho HTTP request

## 2. Thiết lập Axios

File tham chiếu: frontend/src/api/axios.ts

Axios được cấu hình để gửi yêu cầu tới API backend với base URL lấy từ biến môi trường.
Cấu hình bao gồm header mặc định cho JSON và cho phép gửi credentials để đính kèm cookie hoặc session.

Điểm cần lưu ý:
- Base URL phải phù hợp với môi trường chạy ứng dụng
- Nếu dùng cookie hoặc auth dựa trên session, cần bật gửi credentials
- Header mặc định nên là application/json cho các request dữ liệu

## 3. Định nghĩa API cho profile

File tham chiếu: frontend/src/features/auth/authAPI.ts

Phần API profile bao gồm các chức năng chính:
- Lấy dữ liệu profile của người dùng hiện tại từ API
- Gửi yêu cầu update profile với dữ liệu mới
- Upload avatar dưới dạng multipart form data

Các điểm quan trọng:
- Upload avatar cần dùng multipart/form-data để gửi file
- Endpoint edit profile nên nhận payload chỉ gồm các trường người dùng thay đổi
- Endpoint lấy profile nên trả về thông tin user hiện tại
- Cần có cơ chế xử lý lỗi chung để hiển thị thông báo dễ hiểu

## 4. Quản lý trạng thái với Redux Hook

File tham chiếu: frontend/src/features/auth/authSlice.ts

Redux slice của auth quản lý trạng thái profile và feedback:
- `user`: thông tin người dùng
- `status`: trạng thái async (idle, loading, succeeded, failed)
- `error`: thông báo lỗi
- `message`: thông báo thành công hoặc phản hồi từ server

Async thunks quan trọng:
- Lấy profile khi trang được mở
- Update profile khi người dùng lưu
- Upload avatar khi người dùng chọn file mới

Extra reducers cần xử lý:
- Khi lấy profile thành công, cập nhật state user
- Khi update profile thành công, đồng bộ lại user
- Khi upload avatar thành công, cập nhật avatarUrl trong user

Hooks dùng trong component:
- `useAppDispatch` để gọi action async
- `useAppSelector` để đọc trạng thái auth từ store

## 5. Thiết kế UI trang Edit Profile

File tham chiếu: frontend/src/pages/auth/Profile.tsx

### Cấu trúc chính
1. Sidebar điều hướng cho dashboard/profile/setting
2. Header với tiêu đề và mô tả chức năng
3. Form chỉnh sửa profile
4. Khối avatar preview và upload

### Quy trình dữ liệu
- Khi component mount, gọi action lấy profile từ backend
- Khi profile tải về, đồng bộ giá trị form với dữ liệu user
- Người dùng chỉnh sửa các trường và nhấn Save để gửi cập nhật
- Khi chọn avatar mới, thực hiện upload ngay và hiển thị preview

### Các trường form chính
- Full Name
- Phone Number
- Address
- Bio

Form nên vận hành theo kiểu controlled component để dễ quản lý giá trị và validation.

### Avatar upload
- Hiển thị preview avatar hiện tại hoặc ảnh mặc định khi chưa có
- Nhấn vào khu vực avatar bật input chọn file
- Khi chọn ảnh, tạo preview tạm thời và gửi file lên API
- Cập nhật URL avatar trong state khi upload thành công

### Nút lưu
- Kiểm tra dữ liệu bắt buộc như fullName
- Chuẩn bị payload chỉ bao gồm trường có giá trị
- Gọi action cập nhật profile và xử lý kết quả

## 6. Lưu ý kỹ thuật

- Nếu backend trả avatarUrl là đường dẫn tĩnh, frontend cần chuỗi đúng prefix URL để hiển thị hình ảnh
- Với cấu hình credentials, cần đồng bộ CORS và base URL giữa frontend và backend
- Thông báo trạng thái loading và lỗi nên hiển thị rõ ràng cho người dùng
- Duy trì form controlled component giúp đồng bộ trạng thái tốt hơn với Redux

## 7. Hướng dẫn mở rộng

- Thêm validation chi tiết cho phone, address, bio
- Tách trạng thái loading riêng cho từng action như uploadAvatar hoặc editProfile
- Thêm fallback avatar khi người dùng chưa có ảnh
- Đồng bộ thông tin user với local storage hoặc session nếu cần

## 8. Kết luận

Chức năng Edit Profile trong frontend được chia thành ba phần chính:
- Axios để gọi API và upload avatar
- Redux + async thunk để quản lý trạng thái và xử lý dữ liệu
- UI trang profile với form, preview avatar, điều hướng rõ ràng và phản hồi người dùng

Tài liệu này phù hợp để dùng làm hướng dẫn triển khai hoặc làm base cho chức năng edit profile trong dự án.
