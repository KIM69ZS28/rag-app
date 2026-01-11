# Runbook: ログインできない（Cognito認証失敗）

## 概要
ユーザがログインできない、またはトークン取得に失敗する事象への対応。

## 典型症状
- "NotAuthorizedException"
- "UserNotFoundException"
- JWTの検証エラー

## 対応手順
1. Cognitoの障害有無（AWS Health）確認
2. 該当ユーザが存在するか確認（User Pool）
3. パスワードポリシー/ロック状態を確認
4. API Gateway Authorizerのキャッシュ有無確認
5. クライアントの時刻ズレ（JWT exp/nbf）を疑う
6. 解決しない場合はサポート連携
