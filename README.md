# 🚀 YouTube API ELT Pipeline

---

## 1. Project overview

Đây là một **production-style ELT Data Pipeline** mô phỏng hệ thống thu thập và quản lý dữ liệu phân tích từ **YouTube API**. Mục tiêu tập trung vào:

- Thiết kế scalable ELT architecture
- Xây dựng pipeline có data quality validation
- Triển khai containerized orchestration
- Áp dụng CI/CD cho data workflow
- Đảm bảo tính idempotent & incremental loading

---

## 2. Architecture Overview

<img width="773" height="664" alt="image" src="https://github.com/user-attachments/assets/513ebd4f-8bea-482e-849a-f49602f0701b" />

Pipeline tuân theo mô hình **ELT (Extract → Load → Transform)**.

### Data Flow

1. Extract dữ liệu từ YouTube API bằng Python
2. Load raw data vào `staging schema`
3. Transform và upsert vào `core schema`
4. Validate bằng Data Quality checks
5. Sẵn sàng cho BI/Analytics layer

Thiết kế gồm:

- Containerized Airflow (Docker)
- PostgreSQL (staging + core layer)
- Modular DAG orchestration
- CI/CD với GitHub Actions

---

## 3. Engineering Decisions

### 🔹 Vì sao dùng ELT thay vì ETL?

- Vì hiện tại, chi phí lưu trữ đã trở nên rất rẻ, do đó có thể thực hiện việc Load trước mà không sợ làm lãng phí không gian lưu trữ.

### 🔹 Vì sao tách staging & core schema?

| Layer   | Mục đích                                  |
| ------- | ----------------------------------------- |
| staging | Lưu raw data, phục vụ audit/debug         |
| core    | Chuẩn hóa, clean data, sẵn sàng analytics |

Thiết kế này giúp:

- Tránh làm hỏng raw data
- Dễ reprocess
- Chuẩn mô hình Data Warehouse

### 🔹 Sử dụng Upsert

Được xử lý bằng logic code Python thông qua cấu trúc điều kiện if-else khi lặp qua từng dòng dữ liệu. Script sẽ trích xuất tất cả ID đang có trong bảng thành một list/set và đối chiếu:

- Với mỗi video mới, hệ thống đối chiếu ID với danh sách ID hiện có trong bảng. Nếu ID chưa tồn tại, bản ghi sẽ được chèn vào bảng.
- Trường hợp ID đã tồn tại, hệ thống không tạo bản ghi trùng lặp mà thực hiện cập nhật các trường có thể thay đổi theo thời gian, gồm lượt xem, lượt thích và số bình luận.

---

## 4. Extracted Fields

Lần gọi API đầu tiên sẽ nạp dữ liệu vào hệ thống - đây là bước full upload ban đầu.

Các lần lấy dữ liệu tiếp theo sẽ thực hiện upserts giá trị cho một số variables (cột) nhất định. Một khi core schema đã được nạp đủ dữ liệu và các bước unit test cùng data quality tests được triển khai thành công, dữ liệu sẽ ở trạng thái sẵn sàng để phân tích.

7 variables sau đây được trích xuất từ API:

- Video ID
- Video Title
- Upload Date
- Duration
- Views Count
- Likes Count
- Comments Count

---
