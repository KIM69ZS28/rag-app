# システム構成（AWS）

## 1. 構成概要
- 認証：Amazon Cognito（User Pool）
- API：Amazon API Gateway（REST）
- アプリケーション：AWS Lambda
- DB：Amazon RDS for PostgreSQL
- 監視：Amazon CloudWatch（Logs/Alarms）
- オブジェクト：Amazon S3（監査ログアーカイブ、帳票出力）
- 鍵管理：AWS KMS

## 2. データフロー
1) クライアントはCognitoで認証を行い、IDトークン（JWT）を取得
2) API GatewayへJWT付きでAPIリクエスト
3) API GatewayでCognito Authorizerにより認証
4) Lambdaが処理を実行し、RDSへアクセス
5) 監査対象操作は監査ログをCloudWatch Logsに出力し、日次でS3へアーカイブ

## 3. 監視（例）
- Lambda：Errors、Duration、Throttles
- API Gateway：5XXError、Latency
- RDS：CPUUtilization、DatabaseConnections、FreeStorageSpace

## 4. バックアップ
- RDS：自動バックアップ有効（保持7日）
- 監査ログ：S3に90日保持
