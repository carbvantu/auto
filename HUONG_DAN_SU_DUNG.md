# ORBIT - Hướng Dẫn Sử Dụng
## Công cụ lên lịch đăng video tự động lên Facebook, TikTok và YouTube

---

## 1. GIỚI THIỆU

**ORBIT** là ứng dụng web giúp bạn quản lý và lên lịch đăng video tự động lên nhiều nền tảng mạng xã hội (Facebook, TikTok, YouTube) từ một nơi duy nhất.

---

## 2. CÀI ĐẶT & CHẠY ỨNG DỤNG

### Yêu cầu hệ thống
- **Node.js** v18 trở lên
- **pnpm** v9 trở lên
- **PostgreSQL** database

### Bước 1: Cài đặt pnpm (nếu chưa có)
```bash
npm install -g pnpm
```

### Bước 2: Giải nén và cài đặt dependencies
```bash
tar -xzf orbit-video-scheduler.tar.gz
cd orbit-video-scheduler
pnpm install
```

### Bước 3: Cài đặt biến môi trường
Tạo file `.env` trong thư mục gốc:
```env
DATABASE_URL=postgresql://username:password@localhost:5432/orbit_db
SESSION_SECRET=your-secret-key-here
```

### Bước 4: Tạo database và push schema
```bash
pnpm --filter @workspace/db run push
```

### Bước 5: Chạy ứng dụng
Mở **2 terminal**:

**Terminal 1 - API Server:**
```bash
pnpm --filter @workspace/api-server run dev
```

**Terminal 2 - Frontend:**
```bash
pnpm --filter @workspace/video-scheduler run dev
```

Truy cập: `http://localhost:PORT` (PORT sẽ hiển thị trong terminal)

---

## 3. HƯỚNG DẪN SỬ DỤNG

### 3.1 Dashboard (Trang chủ)
- Xem **tổng số video**, lịch đăng, bài đã đăng, đang chờ, thất bại
- Xem **danh sách bài sắp đăng** (Upcoming Posts)
- Xem **thống kê theo nền tảng** (Facebook / TikTok / YouTube)
- Xem **hoạt động gần đây** (Recent Activity)

### 3.2 Videos (Thư viện video)
1. Nhấn nút **"Add Video"**
2. Điền thông tin:
   - **Title**: Tên video
   - **Description**: Mô tả
   - **File Path**: Đường dẫn file video (ví dụ: `/videos/my-video.mp4`)
   - **Tags**: Các tag phân cách bằng dấu phẩy (ví dụ: `marketing, product`)
3. Nhấn **"Add Video"** để lưu

### 3.3 Schedules (Lịch đăng)
1. Nhấn **"New Schedule"**
2. Chọn **video** từ thư viện
3. Chọn **nền tảng** muốn đăng (có thể chọn nhiều nền tảng cùng lúc)
4. Chọn **ngày và giờ** đăng
5. Nhấn **"Create Schedule"**

Bạn có thể lọc lịch đăng theo:
- Trạng thái: Pending / Published / Failed / Cancelled
- Nền tảng: Facebook / TikTok / YouTube

### 3.4 Platforms (Nền tảng)
1. Nhấn **"Add Platform"**
2. Chọn nền tảng (Facebook / TikTok / YouTube)
3. Điền **Account Name** (tên tài khoản của bạn)
4. Điền **Account ID** (ID tài khoản)
5. Điền **Access Token** (xem phần cấu hình API bên dưới)
6. Nhấn **"Add Platform"**

Bạn có thể bật/tắt từng tài khoản bằng công tắc (toggle) trên card.

### 3.5 Settings (Cài đặt)
Trang này hiển thị hướng dẫn cấu hình API cho từng nền tảng.

---

## 4. CẤU HÌNH API ĐỂ TỰ ĐỘNG ĐĂNG BÀI

Để ứng dụng thực sự tự động đăng video, bạn cần lấy API credentials từ mỗi nền tảng:

### 4.1 Facebook
1. Vào [Facebook Developers](https://developers.facebook.com/)
2. Tạo một **Facebook App** mới
3. Thêm **Facebook Login** và **Pages API**
4. Yêu cầu quyền: `pages_manage_posts`, `pages_read_engagement`
5. Lấy **Page Access Token** (dài hạn - Long-lived token)
6. Điền token vào mục **Access Token** khi thêm platform Facebook

### 4.2 TikTok
1. Vào [TikTok for Developers](https://developers.tiktok.com/)
2. Tạo tài khoản Developer
3. Đăng ký ứng dụng và yêu cầu **Content Posting API**
4. Sau khi được duyệt, lấy **Access Token** qua OAuth flow
5. Điền token vào mục **Access Token** khi thêm platform TikTok

### 4.3 YouTube
1. Vào [Google Cloud Console](https://console.cloud.google.com/)
2. Tạo project mới, bật **YouTube Data API v3**
3. Tạo **OAuth 2.0 credentials**
4. Cài đặt **OAuth consent screen**
5. Chạy OAuth flow để lấy **Refresh Token**
6. Điền token vào mục **Access Token** khi thêm platform YouTube

---

## 5. TÍCH HỢP TỰ ĐỘNG ĐĂNG BÀI

Hiện tại hệ thống quản lý lịch và lưu trữ schedules hoạt động đầy đủ. Để bật tính năng tự động đăng bài theo lịch, bạn cần thêm một **cron job** vào file `artifacts/api-server/src/`:

```typescript
// Ví dụ: Thêm cron job chạy mỗi phút để kiểm tra và đăng bài
// Cài thêm: pnpm --filter @workspace/api-server add node-cron
import cron from 'node-cron';

cron.schedule('* * * * *', async () => {
  // Tìm các schedule có status = 'pending' và scheduledAt <= now
  // Gọi API của từng nền tảng để đăng video
  // Cập nhật status thành 'published' hoặc 'failed'
});
```

---

## 6. CẤU TRÚC DỰ ÁN

```
orbit-video-scheduler/
├── artifacts/
│   ├── api-server/        # Backend Express API
│   │   └── src/routes/    # Các API route
│   └── video-scheduler/   # Frontend React + Vite
│       └── src/pages/     # Các trang: dashboard, videos, schedules, platforms
├── lib/
│   ├── db/                # Database schema (Drizzle ORM)
│   ├── api-spec/          # OpenAPI spec
│   ├── api-client-react/  # Generated React Query hooks
│   └── api-zod/           # Generated Zod validation schemas
└── package.json
```

---

## 7. CÁC LỆNH HỮU ÍCH

```bash
# Typecheck toàn bộ project
pnpm run typecheck

# Build toàn bộ project
pnpm run build

# Regenerate API hooks sau khi thay đổi OpenAPI spec
pnpm --filter @workspace/api-spec run codegen

# Push DB schema changes
pnpm --filter @workspace/db run push
```

---

## 8. LIÊN HỆ & HỖ TRỢ

Ứng dụng được xây dựng trên Replit. Nếu cần thêm tính năng hoặc hỗ trợ tích hợp API thực tế với Facebook/TikTok/YouTube, hãy liên hệ để được hướng dẫn thêm.
