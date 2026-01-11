# Runbook: RDS接続エラー

## 症状
- "too many connections"
- "timeout"
- "could not connect to server"

## 対応手順
1. RDS DatabaseConnections を確認
2. Lambdaの同時実行数が急増していないか確認
3. アプリのコネクションプール設定を確認
4. スロークエリ/ロックを疑いPerformance Insightsを確認
5. 一時対応
   - Lambda同時実行制限
   - 不要バッチ停止
   - 影響が甚大な場合のみ再起動検討

## 恒久対応
- 接続プール導入
- クエリ改善、インデックス調整
- リードレプリカ検討
