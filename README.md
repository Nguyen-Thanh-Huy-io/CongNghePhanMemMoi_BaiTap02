# Edit Profile - PubliCast

Tài liệu này tập trung vào chức năng Edit Profile trong dự án PubliCast, chia thành 2 bài tập chính: Backend (Bài tập 1) và Frontend (Bài tập 2).

---

## 🎯 Bài tập 1: Backend - Edit Profile API

📌 **Mô tả chức năng**

Chức năng Edit Profile cho phép người dùng đã đăng nhập cập nhật thông tin hồ sơ cá nhân thông qua API backend.

**Các trường có thể cập nhật:**
- `fullName`: Tên đầy đủ
- `avatarUrl`: URL ảnh đại diện
- `phone`: Số điện thoại (tùy chọn)
- `address`: Địa chỉ (tùy chọn)
- `bio`: Tiểu sử (tùy chọn)

### 🔐 Yêu cầu xác thực

- **Authentication**: Required (JWT token hoặc Cookie)
- **Authorization**: Chỉ người dùng đã đăng nhập mới được phép sửa profile của họ

### 🔧 Endpoint

```
PUT /api/profile/edit
```

### 📨 Request body

```json
{
  "fullName": "Thanh Huy 2",
  "phone": "0912345678",
  "address": "123 Main St, City",
  "bio": "Streamer and content creator",
  "avatarUrl": "https://example.com/avatar.jpg"
}
```

### ✅ Validation rules

- `fullName`: Optional, 2-100 ký tự
- `phone`: Optional, định dạng số điện thoại hợp lệ
- `address`: Optional, tối đa 255 ký tự
- `bio`: Optional, tối đa 500 ký tự
- `avatarUrl`: Optional, phải là URL hợp lệ

### ✅ Response mẫu

**200 OK**

```json
{
  "message": "Profile updated successfully",
  "data": {
    "id": "uuid-here",
    "email": "user@example.com",
    "fullName": "Thanh Huy 2",
    "phone": "0912345678",
    "address": "123 Main St, City",
    "bio": "Streamer and content creator",
    "avatarUrl": "https://example.com/avatar.jpg",
    "role": "USER",
    "status": "ACTIVE",
    "createdAt": "2026-05-09T...",
    "updatedAt": "2026-05-10T..."
  }
}
```

### ⚠️ Lỗi thường gặp

| Status | Error | Mô tả |
|--------|-------|-------|
| 400 | Bad Request | Dữ liệu validation không hợp lệ |
| 401 | Unauthorized | Thiếu hoặc token không hợp lệ |
| 403 | Forbidden | Tài khoản bị cấm hoặc không có quyền |
| 404 | Not Found | Người dùng không tồn tại |
| 500 | Internal Server Error | Lỗi server |

### 🧩 Kiến trúc Backend liên quan

**Các file chính:**

- `src/controllers/profile.controller.js` - Xử lý logic HTTP
- `src/services/profile.service.js` - Business logic
- `src/repositories/user.repository.js` - Truy vấn database
- `src/middlewares/auth.middleware.js` - Xác thực JWT/Cookie
- `src/routes/profile.routes.js` - Định nghĩa routes
- `prisma/schema.prisma` - Database schema

---

## 🎯 Bài tập 2: Frontend - Edit Profile Implementation

📌 **Mô tả chức năng**

Triển khai chức năng Edit Profile ở phía frontend với React, Redux Hooks, Axios và TailwindCSS.

### 1. Cấu trúc Axios API

**File:** `frontend/src/api/axios.ts`

Axios được cấu hình để gửi request tới backend với:
- Base URL lấy từ biến môi trường
- Headers mặc định cho JSON
- Cho phép gửi credentials (cookie/session)

**Điểm cần lưu ý:**
- Base URL phải phù hợp với môi trường chạy ứng dụng
- Nếu dùng cookie-based auth, cần bật `withCredentials`
- Header mặc định nên là `application/json`

### 2. Định nghĩa API endpoints

**File:** `frontend/src/features/auth/authAPI.ts`

Các hàm API chính:

- `getUserProfileRequest()` - Lấy dữ liệu profile hiện tại từ `/user/profile`
- `editProfileRequest(payload)` - Gửi cập nhật profile tới `/profile/edit` (PUT)
- `uploadAvatarRequest(file)` - Upload avatar tới `/upload/avatar` (POST multipart)

**Điểm quan trọng:**
- Upload avatar cần dùng `multipart/form-data`
- Endpoint edit profile nhận payload chỉ gồm các trường được thay đổi
- Cần có cơ chế xử lý lỗi chung để hiển thị thông báo user-friendly

### 3. Quản lý trạng thái với Redux Hook

**File:** `frontend/src/features/auth/authSlice.ts`

**State chính:**
- `user`: Thông tin người dùng
- `status`: Trạng thái async (`idle | loading | succeeded | failed`)
- `error`: Thông báo lỗi
- `message`: Thông báo thành công từ server

**Async thunks:**
- `getUserProfile` - Lấy profile khi trang được mở
- `editProfile` - Cập nhật profile khi người dùng lưu
- `uploadAvatar` - Upload avatar khi người dùng chọn file mới

**Extra reducers xử lý:**
- Khi lấy profile thành công → cập nhật `state.user`
- Khi update profile thành công → đồng bộ lại `state.user`
- Khi upload avatar thành công → cập nhật `user.avatarUrl`

**Redux Hooks:**

File `frontend/src/hooks.ts` cung cấp:
- `useAppDispatch` - Gọi action async
- `useAppSelector` - Đọc trạng thái từ store

Dùng trong component:
```
const dispatch = useAppDispatch();
const user = useAppSelector((s) => s.auth.user);
```

### 4. Thiết kế UI trang Edit Profile

**File:** `frontend/src/pages/auth/Profile.tsx`

**Cấu trúc chính:**
1. Sidebar điều hướng (Dashboard, Profile, Stream Keys, Settings)
2. Header với tiêu đề và mô tả chức năng
3. Form chỉnh sửa profile với các trường: Full Name, Phone, Address, Bio
4. Khối avatar preview và upload

**Quy trình dữ liệu:**
- Component mount → gọi `getUserProfile()` từ backend
- Khi profile tải về → đồng bộ giá trị form với dữ liệu user
- Người dùng chỉnh sửa form → nhấn Save → gọi `editProfile(payload)`
- Khi chọn avatar mới → upload ngay → hiển thị preview

**Form fields:**
- Full Name (bắt buộc)
- Phone Number
- Address
- Bio

Form sử dụng kiểu controlled component để dễ quản lý giá trị.

**Avatar upload flow:**
- Hiển thị preview avatar hiện tại hoặc ảnh mặc định
- Nhấn vào khu vực avatar → kích hoạt input file ẩn
- Chọn ảnh → tạo preview tạm thời với `URL.createObjectURL(file)`
- Gửi file lên API ngay
- Cập nhật `avatarUrl` trong state khi upload thành công

**Nút lưu:**
- Kiểm tra dữ liệu bắt buộc (fullName)
- Chuẩn bị payload chỉ bao gồm trường có giá trị
- Gọi action cập nhật profile
- Xử lý kết quả thành công/lỗi

### 5. Lưu ý kỹ thuật Frontend

- Nếu backend trả `avatarUrl` là đường dẫn tĩnh, cần chuỗi đúng prefix URL
- Với cấu hình credentials, cần đồng bộ CORS giữa frontend/backend
- Thông báo loading và lỗi nên hiển thị rõ ràng cho user (toast notification)
- Duy trì form controlled component giúp đồng bộ tốt với Redux state
- Nên cleanup object URL khi component unmount để tránh memory leak

### 6. Hướng dẫn mở rộng

- Thêm validation chi tiết cho phone, address, bio
- Tách trạng thái loading riêng cho từng action
- Thêm fallback avatar khi user chưa có ảnh
- Đồng bộ user info với local storage hoặc session nếu cần
- Thêm tính năng crop/resize avatar trước khi upload
- Implement undo/redo chức năng cho form

---

## 📚 Liên kết tài liệu

- [Frontend Edit Profile Guide](docs/frontend/EDIT_PROFILE.md) - Chi tiết frontend implementation
- [Backend API Reference](docs/backend/API_REFERENCE.md) - Danh sách API endpoints
- [Main README](README.md) - Tổng quan dự án

---

## ✅ Checklist hoàn thành

**Bài tập 1 (Backend):**
- [ ] Endpoint PUT `/api/profile/edit` hoạt động
- [ ] Validation dữ liệu input
- [ ] Authentication middleware
- [ ] Update database thành công
- [ ] Response format đúng
- [ ] Error handling đầy đủ

**Bài tập 2 (Frontend):**
- [ ] Axios instance cấu hình đúng
- [ ] Redux API functions định nghĩa
- [ ] Redux thunks tạo được
- [ ] Form UI hiển thị đúng
- [ ] Avatar upload hoạt động
- [ ] Save profile thành công
- [ ] Error notification hiển thị

---

