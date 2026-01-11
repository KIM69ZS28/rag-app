# API仕様（抜粋）

## 認証
- Cognito JWT必須
- header: Authorization: Bearer <token>

## 1) 打刻：出勤
POST /v1/attendance/clock-in
request:
{
  "timestamp": "2026-01-11T09:01:00+09:00"
}
response:
{
  "attendance_id": "att_123",
  "status": "CLOCKED_IN"
}

## 2) 打刻：退勤
POST /v1/attendance/clock-out
request:
{
  "timestamp": "2026-01-11T18:05:00+09:00"
}

## 3) 月次勤怠の申請
POST /v1/attendance/monthly/apply
request:
{
  "month": "2026-01",
  "comment": "申請します"
}

## 4) 承認
POST /v1/attendance/monthly/{apply_id}/approve
- MANAGERのみ実行可能

## 5) ADMIN代理修正
POST /v1/admin/attendance/{attendance_id}/override
- ADMINのみ実行可能
- 監査ログ必須
