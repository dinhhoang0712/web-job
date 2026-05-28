## Web JobHunter

Web JobHunter là ứng dụng web tuyển dụng (job board) mẫu, bao gồm backend viết bằng Spring Boot (Java) và frontend viết bằng React + Vite (TypeScript). Tài liệu này mô tả nhanh cấu trúc dự án, các công nghệ chính, và hướng dẫn chạy/chạy thử trên máy local.

**Mục tiêu:** cung cấp nền tảng để quản lý công việc, công ty, hồ sơ ứng viên, và quyền truy cập (role/permission).

---

**Nội dung chính của README (tóm tắt):**

- **Cài đặt môi trường**: Java 17, Gradle, Node.js, npm/yarn, MySQL.
- **Chạy dev**: backend bằng `gradlew bootRun`, frontend bằng `npm run dev`.
- **Build & Deploy**: hướng dẫn build cho production và đề xuất Docker / Docker Compose.

---

## Công nghệ chính

- Backend: Java 17, Spring Boot (3.x), Spring Data JPA, Spring Security, Thymeleaf
- Frontend: React 18, TypeScript, Vite, Ant Design, Redux Toolkit
- Database: MySQL (hoặc MariaDB) — kết nối qua `mysql-connector-java`
- OpenAPI/Swagger: `springdoc-openapi` để xem tài liệu API

## Cấu trúc repository

- `backend/` — mã nguồn Spring Boot, cấu hình Gradle (Kotlin DSL). Tập trung vào API, security, và template Thymeleaf.
- `frontend/` — mã nguồn React + Vite (TypeScript). Chứa components, pages, redux slices, styles.
- `metadata/` — cấu hình OpenAPI và các mẫu email/templace tài liệu.

## Yêu cầu trước khi chạy

- Java 17 (JDK 17) đã cài đặt và `JAVA_HOME` trỏ đúng.
- Node.js (lời khuyên Node 18+) và `npm` hoặc `yarn`.
- MySQL server (tạo database và user theo cấu hình ứng dụng).

## Cấu hình môi trường

- Backend: cấu hình nằm ở `backend/src/main/resources/application.properties`.
  - Tham số quan trọng: `spring.datasource.url`, `spring.datasource.username`, `spring.datasource.password`, `server.port`.
  - Để chạy local nhanh, bạn có thể tạo file `application-local.properties` hoặc sử dụng biến môi trường.
- Frontend: Vite sử dụng các biến bắt đầu bằng `VITE_` (ví dụ `VITE_API_BASE_URL`). Xem `frontend/vite.config.ts` để biết chi tiết.

## Chạy dự án trên máy local (hướng dẫn chi tiết)

1. Chuẩn bị database (MySQL)

- Tạo database mới, ví dụ `jobhunter_db`.
- Tạo user và cấp quyền:

```sql
CREATE DATABASE jobhunter_db;
CREATE USER 'jobuser'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON jobhunter_db.* TO 'jobuser'@'localhost';
FLUSH PRIVILEGES;
```

2. Chạy backend

- Mở terminal trong thư mục `backend`.
- Cập nhật `backend/src/main/resources/application.properties` (hoặc thiết lập biến môi trường) cho chuỗi kết nối MySQL.
- Chạy lệnh (Windows):

```powershell
cd backend
.\gradlew.bat bootRun
```

Sau khi chạy, backend mặc định lắng nghe cổng được cấu hình trong `application.properties` (thường là `8080`).

3. Chạy frontend

- Mở terminal khác, vào thư mục `frontend`.
- Cài dependencies và chạy dev server:

```bash
cd frontend
npm install
npm run dev
```

- Mặc định Vite sẽ chạy trên `http://localhost:5173` (hoặc port được chỉ định). Frontend sẽ gọi API theo `VITE_API_BASE_URL`.

4. Truy cập giao diện

- Mở trình duyệt vào `http://localhost:5173` (hoặc `http://localhost:3000` nếu cấu hình khác).

## Build production

- Backend:

```powershell
cd backend
.\gradlew.bat clean build
```

- Frontend:

```bash
cd frontend
npm run build
```

- Kết quả frontend build sẽ nằm trong `frontend/dist` (hoặc thư mục do Vite cấu hình). Bạn có thể cấu hình backend để phục vụ các file tĩnh này (ví dụ copy vào `backend/src/main/resources/static`).

## API & Tài liệu

- Dự án tích hợp `springdoc-openapi`. Sau khi chạy backend, truy cập UI Swagger thường ở:

- `http://localhost:8080/swagger-ui.html` hoặc `http://localhost:8080/swagger-ui/index.html` (tùy cấu hình).

Ghi chú: Kiểm tra `metadata/OpenAPIConfig.java` để biết cấu hình base path và các thông tin mô tả API.

## Docker (đề xuất)

- Bạn có thể thêm `Dockerfile` cho backend (đã có `backend/Dockerfile`) và một `Dockerfile` cho frontend, sau đó viết `docker-compose.yml` để khởi tạo MySQL + backend + frontend cho môi trường local hoặc staging.

Ví dụ khái niệm:

```yaml
version: '3.8'
services:
	db:
		image: mysql:8
		environment:
			MYSQL_DATABASE: jobhunter_db
			MYSQL_USER: jobuser
			MYSQL_PASSWORD: password
			MYSQL_ROOT_PASSWORD: rootpassword
		ports:
			- '3306:3306'
	backend:
		build: ./backend
		depends_on: [db]
		environment:
			SPRING_DATASOURCE_URL: jdbc:mysql://db:3306/jobhunter_db
			SPRING_DATASOURCE_USERNAME: jobuser
			SPRING_DATASOURCE_PASSWORD: password
	frontend:
		build: ./frontend
		ports:
			- '5173:5173'
```

## Kiểm thử & phát triển

- Mã backend có cấu trúc test theo Gradle — chạy `./gradlew test` để chạy unit/integration tests.
- Frontend có thể có script `npm test` nếu cấu hình testing (Jest / React Testing Library).

## Gợi ý phát triển

- Tạo file `README-backend.md` và `README-frontend.md` nếu muốn tách hướng dẫn chi tiết hơn.
- Thêm script Docker Compose để khởi động toàn bộ hệ thống cho dev.
- Đặt secrets/credentials vào biến môi trường hoặc secret manager, không commit trực tiếp vào mã nguồn.

## Đóng góp

- Muốn đóng góp: fork repo, tạo branch mới, làm thay đổi, tạo pull request mô tả rõ ràng.
- Tuân theo coding style hiện có (Gradle Kotlin DSL cho backend, ESLint/Prettier cho frontend nếu có).

---

Nếu bạn muốn tôi tách README này thành hai file `README-backend.md` và `README-frontend.md`, hoặc thêm hướng dẫn Docker Compose chi tiết, cho biết tôi sẽ tiếp tục thực hiện.
