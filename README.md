Edit Profile - PubliCast
Tài liệu này chỉ tập trung vào chức năng Edit Profile trong dự án PubliCast.

📌 Mô tả chức năng
Chức năng Edit Profile cho phép người dùng đã đăng nhập cập nhật thông tin hồ sơ cá nhân.

Các trường có thể cập nhật:

fullName: Tên đầy đủ
avatarUrl: URL ảnh đại diện
🔐 Yêu cầu xác thực
Authentication: Required (JWT token)
Authorization: Chỉ người dùng đã đăng nhập mới được phép sửa profile
🔧 Endpoint
PUT /api/profile/edit
Request body

{
  "fullName": "Thanh Huy 2",
  "avatarUrl": "https://example.com/avatar.jpg"
}
Validation rules

fullName: Optional, 2-100 ký tự
avatarUrl: Optional, phải là URL hợp lệ
✅ Response mẫu
200 OK

{
  "message": "Profile updated successfully",
  "data": {
    "id": "uuid-here",
    "email": "user@example.com",
    "fullName": "Thanh Huy 2",
    "avatarUrl": "https://example.com/avatar.jpg",
    "role": "USER",
    "status": "ACTIVE",
    "createdAt": "2026-05-09T...",
    "updatedAt": "2026-05-10T..."
  }
}
⚠️ Lỗi thường gặp
400 Bad Request: Dữ liệu validation không hợp lệ
401 Unauthorized: Thiếu hoặc token không hợp lệ
403 Forbidden: Tài khoản bị cấm hoặc không có quyền
404 Not Found: Người dùng không tồn tại
500 Internal Server Error: Lỗi server
🧩 Kiến trúc liên quan
Các file chính liên quan đến chức năng Edit Profile:

src/controllers/profile.controller.js
src/services/profile.service.js
src/repositories/user.repository.js
src/middlewares/auth.middleware.js
src/routes/profile.routes.js
src/prisma.js
📌 Ghi chú
Tài liệu này chỉ dành cho phần Edit Profile. Các chức năng khác trong dự án không nằm trong phạm vi của file README này.
