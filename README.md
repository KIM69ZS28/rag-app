## 構成図

- Ingestion: ![](diagrams/ingestion.png)
- Inference: ![](diagrams/inference.png)

Editable source files are also available:
- `diagrams/ingestion.drawio`
- `diagrams/inference.drawio`

## API（Inference）: `/ask`

本ポートフォリオでは、RAG（Retrieval-Augmented Generation）構成の質問応答APIを API Gateway + Lambda で公開しています。  
質問文をPOSTすると、DynamoDBに格納されたドキュメントチャンク（embedding付き）から関連情報（TopK）を検索し、Bedrock（LLM）で根拠に基づく回答を生成して返します。

また、RAG + Bedrock + DynamoDB（Embedding）構成を、Amazon Cognito（JWT）で保護することで、実運用を想定した認証付きQ&A APIとして実装しました。
---

### Endpoint

- Method: `POST`
- Path: `/ask`

Example:
```
https://ui2eu8z8bb.execute-api.ap-northeast-1.amazonaws.com/prod/ask
```

---

### Request Example (curl / Auth flow)

1) CognitoでIDトークンを取得（例）

```bash
aws cognito-idp initiate-auth \
  --region ap-northeast-1 \
  --client-id <COGNITO_APP_CLIENT_ID> \
  --auth-flow USER_PASSWORD_AUTH \
  --auth-parameters USERNAME="<EMAIL>",PASSWORD="<PASSWORD>"
```
レスポンスの AuthenticationResult.IdToken を控えます。

2) APIを呼び出し

```bash
curl -s -X POST "https://ui2eu8z8bb.execute-api.ap-northeast-1.amazonaws.com/prod/ask" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <ID_TOKEN>" \
  -d '{"question":"勤怠締め後に勤怠を修正したい場合、誰が対応できますか？監査上の注意点も教えてください。"}'
```
---

### Request Example (Browser / UI test)

ブラウザからのCORS確認・簡易UIとして `test.html` を用意しています。

Run local server:

```bash
python3 -m http.server 8000
```

Open:

```
http://localhost:8000/test.html
```

---

### Response Format

レスポンスは厳密なJSON形式で返却されます。

- answer: 主張（claim）の配列。各claimには参照した source_id を紐付け
- unknowns: 根拠不足で断定できない事項の配列（推測で埋めない）
- sources: 参照ソースの一覧（doc_id / chunk_id / excerpt を含む）

Response example:

```json
{
  "status": "ok",
  "answer": [
    {
      "claim": "勤怠締め後の修正はADMIN（管理者）のみが対応できます。",
      "sources": ["S1", "S2"]
    }
  ],
  "unknowns": [],
  "sources": [
    {
      "source_id": "S1",
      "doc_id": "docs/13_troubleshooting_faq.md",
      "chunk_id": "0002",
      "score": 0.69,
      "excerpt": "..."
    }
  ]
}
```
