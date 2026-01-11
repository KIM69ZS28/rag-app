# Runbook: API 5xxエラー増加

## 概要
API Gatewayの5xxが増加した場合の対応手順。

## 影響
打刻・申請などの主要機能が利用不可となる可能性がある。

## 優先度
P1（即時対応）

## 対応手順
1. CloudWatch Alarmを確認（API Gateway 5XXError）
2. Lambda Errors/Durationsを確認
3. 直近デプロイの有無を確認
4. DB接続数を確認（RDS DatabaseConnections）
5. エラー内容に応じて切り分け
   - タイムアウト：Lambda timeout / DB遅延
   - 認証系：Cognito Authorizer
6. 一時復旧策
   - Lambdaの同時実行数制限/メモリ調整
   - DBの接続枯渇なら再起動は最終手段
7. 監査ログ：対応内容を必ず記録

## エスカレーション
30分以内に復旧見込みが立たない場合、オンコール責任者へ連絡。
