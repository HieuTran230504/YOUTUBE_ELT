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
