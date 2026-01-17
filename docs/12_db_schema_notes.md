# DB設計メモ（勤怠管理SaaS）

## 1. 概要
本書はRDS(PostgreSQL)のテーブル設計方針および論理仕様をまとめる。
実装段階ではDBマイグレーションツール（例：Flyway等）を採用する。

## 2. テーブル一覧（例）
### 2.1 users
- user_id (PK)
- email
- name
- department_id
- role (EMPLOYEE/MANAGER/ADMIN)
- created_at

### 2.2 attendance_records
- attendance_id (PK)
- user_id
- work_date
- clock_in_time
- clock_out_time
- break_minutes
- work_minutes
- status (DRAFT/APPLIED/APPROVED/LOCKED)
- updated_at

### 2.3 leave_requests
- request_id (PK)
- user_id
- leave_date
- unit (FULL/HALF_AM/HALF_PM)
- status (APPLIED/APPROVED/REJECTED/CANCELED)
- comment
- updated_at

## 3. 制約・注意点
- 勤怠データの一意制約：user_id + work_date
- 退勤が無い状態で申請は不可
- 有給（FULL）と打刻は共存できない
- 承認後（LOCKED）の更新はADMINのみ可能

## 4. 性能・インデックス方針
- attendance_recordsは月次集計で必ず絞るため、work_dateにインデックス推奨
- user_id + work_date の複合インデックス推奨
- スロークエリはCloudWatch/Performance Insightsで追跡する
