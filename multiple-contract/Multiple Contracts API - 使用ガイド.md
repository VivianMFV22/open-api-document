# Multiple Contracts API - 使用ガイド

## 📚 API一覧

| # | メソッド | エンドポイント | 説明 | ステップ1 | ステップ2 | ステップ4 |
|---|--------|----------|-----|:-------:|:-------:|:-------:|
| 1 | **GET** | `/users` | ユーザー一覧の取得 | ✅ | ❌ | ❌ |
| 2 | **GET** | `/document_types` | 書類種別一覧の取得 | ✅ | ❌ | ❌ |
| 3 | **GET** | `/contract_types` | 契約種別一覧の取得 | ✅ | ❌ | ❌ |
| 4 | **GET** | `/workflow_templates` | ワークフロー一覧の取得 | ✅ | ❌ | ❌ |
| 5 | **POST** | `/multiple_contracts` | 複数契約の作成 | ✅ | ❌ | ❌ |
| 6 | **PUT** | `/multiple_contracts/{ID}` | 複数契約の更新 | ✅ | ❌ | ❌ |
| 7 | **GET** | `/multiple_contracts/{ID}` | 複数契約詳細の取得 | ✅ | ✅ | ✅ |
| 8 | **GET** | `/multiple_contracts/{ID}/contracts/{ID}` | 個別契約詳細の取得 | ✅ | ✅ | ✅ |
| 9 | **GET** | `/multiple_contracts` | 複数契約一覧の取得 | ✅ | ✅ | ✅ |
| 10 | **DELETE** | `/multiple_contracts/{ID}` | 複数契約の削除 | ✅ | ✅ | ✅ |
| 11 | **POST** | `/multiple_contracts/{ID}/contracts/{ID}/documents` | ファイルのアップロード | ✅ | ❌ | ❌ |
| 12 | **PUT** | `/multiple_contracts/{ID}/fields` | 契約フィールドの保存 | ✅ | ❌ | ❌ |
| 13 | **POST** | `/multiple_contracts/{ID}/partner_companies` | パートナー会社の保存 | ❌ | ✅ | ❌ |
| 14 | **PUT** | `/multiple_contracts/{ID}/contracts/{ID}/partner_representative_names` | 個別契約の代表者名更新 | ❌ | ✅ | ❌ |
| 15 | **POST** | `/multiple_contracts/{ID}/confirm` | 複数契約の送信 | ❌ | ❌ | ✅ |
| 16 | **POST** | `/multiple_contracts/{ID}/remind` | リマインド送信 | ❌ | ❌ | ✅ |
| 17 | **POST** | `/multiple_contracts/{ID}/withdraw` | 署名依頼の取下げ | ❌ | ❌ | ✅ |
| 18 | **GET** | `/multiple_contracts/{ID}/certificates` | 証明書のダウンロード | ❌ | ❌ | ✅ |

---

## 🔑 前提条件

### ベースURL
```
https://api.contract.moneyforward.com/v1/
```

### 認証
すべてのAPI呼び出しには認証ヘッダーが必要です：
- `Authorization: Bearer ${アクセストークン}`
- `x-email: ${ユーザーのメールアドレス}`
- `x-token: ${アクセストークン}`

### 制限事項
- **最小契約数:** 2件
- **最大契約数:** 10件
- **ファイル制限:** 契約毎に1つのPDFファイルのみ
- **通知メール:** バッチごとに1回のみ送信

---

## 📋 ステップ1: アップロード & 基本設定

### 1.1 必要な基本情報を取得

#### 基本情報APIs
**`GET /users`** - ユーザー一覧の取得  
**`GET /document_types`** - 書類種別一覧の取得  
**`GET /contract_types`** - 契約種別一覧の取得  
**`GET /workflow_templates`** - ワークフロー一覧の取得

### 1.2 複数契約の作成

**`POST /multiple_contracts`**

**必須パラメータ:**
- `person_in_charge_id`: 担当者ID
- `workflow_template_id`: ワークフローテンプレートID
- `contracts`: 契約配列（2-10件）
  - `name`: 契約名
  - `contract_type_id`: 契約種別ID
  - `document_type_id`: 書類種別ID

**共通設定項目:**
- ワークフローテンプレート
- パートナー会社（代表者名は除く）
- 契約方法（電子契約のみ）
- 担当者
- 備考・コメント

### 1.3 複数契約の更新

**`PUT /multiple_contracts/{ID}`**

**機能:**
- 既存契約の更新
- 新規契約の追加
- 共通プロパティの反映

**リクエスト例:**
```json
{
    "person_in_charge_id": "pic_id",
    "workflow_template_id": "workflow_template_id",
    "contracts": [
        {
            "id": "contract_1_id", // 既存契約の更新
            "name": "contract_1",
            "contract_type_id": "contract_type_id_1"
        },
        {
            "id": null, // 新規契約の追加
            "name": "contract_2",
            "contract_type_id": "contract_type_id_2"
        }
    ]
}
```

### 1.4 ファイルのアップロード

**`POST /multiple_contracts/{ID}/contracts/{CONTRACT_ID}/documents`**

**制限:**
- 契約毎に1つのAPIコール
- PDFファイルのみ
- multipart/form-data形式

### 1.5 契約フィールドの設定

**`PUT /multiple_contracts/{ID}/fields`**

**機能:**
- 既存契約のフィールド編集
- 送信されない契約は変更されない

**リクエスト例:**
```json
[
    {
        "id": "contract_1_id",
        "contract_fields": [
            {
                "id": "field_1_id",
                "name": "自動更新の有無",
                "value": {
                    "data": "anything"
                }
            }
        ]
    }
]
```

---

## 🎨 ステップ2: ワークフロー & パートナー選択

### 2.1 パートナー会社情報の保存

**`POST /multiple_contracts/{ID}/partner_companies`**

**特徴:**
- パートナー情報（代表者名以外）は全契約で共通
- 代表者名は全契約に反映後、個別で更新可能

**リクエスト例:**
```json
[
    {
        "name": "partner 1",
        "representative_name": "representative name 1",
        "approvers": [
            {
                "email": "partner@email.com",
                "name": "approver 1",
                "company_name": "partner 1",
                "access_key": "123456",
                "locale": "ja"
            }
        ]
    }
]
```

### 2.2 個別契約の代表者名更新

**`PUT /multiple_contracts/{ID}/contracts/{CONTRACT_ID}/partner_representative_names`**

**重要:**
- パートナー会社の順序と一致させる必要がある
- 個別契約ごとに異なる代表者名を設定可能

**リクエスト例:**
```json
{
    "representative_names": [
        "repname_1", // partner 1
        "repname_2"  // partner 2
    ]
}
```

---

## 🚀 ステップ4: 契約の送信・管理

### 4.1 複数契約の送信

**`POST /multiple_contracts/{ID}/confirm`**

**機能:**
- 全ての複数契約を一括で承認送信
- 必要な情報が全て設定されていることを確認

### 4.2 リマインド送信

**`POST /multiple_contracts/{ID}/remind`**

**機能:**
- 複数契約のリマインドを一括送信
- 1回のAPIコールで全契約にリマインド

### 4.3 署名依頼の取下げ

**`POST /multiple_contracts/{ID}/withdraw`**

**リクエスト例:**
```json
{
    "comment": "取下げ理由"
}
```

### 4.4 証明書のダウンロード

**`GET /multiple_contracts/{ID}/certificates`**

**オプション:**
- `contract_id`パラメータで個別ファイル取得
- パラメータなしで全ファイルをZIP形式で取得

**レスポンス:**
- **個別ファイル:** PDF形式のバイナリ
- **全ファイル:** ZIP形式のバイナリ

**ZIPファイル構造:**
```
<main-contracts.name> (他<total-contracts>件)/
├── <contracts_1.name>/
│   └── certificate.pdf
└── <contracts_2.name>/
    └── certificate.pdf
```

---

## 📊 状態確認・管理用API

### 複数契約一覧の取得
**`GET /multiple_contracts`**

### 複数契約詳細の取得
**`GET /multiple_contracts/{ID}`**

### 個別契約詳細の取得
**`GET /multiple_contracts/{ID}/contracts/{CONTRACT_ID}`**

### 複数契約の削除
**`DELETE /multiple_contracts/{ID}`**

**機能:**
- 全契約を一括削除のみ可能
- 削除後は取得不可

---

## ⚠️ 重要な制限事項・エラー

### バリデーション

1. **契約数制限**
   - 最小2件、最大10件
   - 範囲外の場合: `400 - Bad Request`

2. **権限チェック**
   - 他オフィス作成契約: `404 - Contract not found`
   - 申請者以外のユーザー: `404 - Contract not found`

3. **代表者名制限**
   - パートナー会社数と不一致: `400 - Bad Request`

### 共通設定要件

**全契約で同一である必要がある項目:**
- ワークフローテンプレート
- パートナー会社（代表者名除く）
- 契約方法
- 担当者
- 備考・コメント

**不一致の場合:** ステップ2に進めずエラー

---

## 🔄 ワークフロー概要

### 完全なフロー
```
ステップ1: 基本情報取得 → 契約作成 → ファイルアップロード → フィールド設定
    ↓
ステップ2: パートナー情報設定 → 代表者名個別設定
    ↓
ステップ4: 契約送信 → リマインド → 取下げ → 証明書ダウンロード
```

### API呼び出し数
- **ステップ1:** 4 (情報取得) + 1 (作成) + N (ファイル) + 1 (フィールド) = 6+N回
- **ステップ2:** 1 (パートナー) + M (代表者名) = 1+M回  
- **ステップ4:** 1-4回（送信、リマインド、取下げ、証明書）

---

## 📚 API仕様書

**完全なAPI仕様:** https://api.contract.moneyforward.com/v1/docs/index.html

**参考資料:**
- Jira: STL-6518, STL-6519
- Confluence: https://moneyforwardvietnam.atlassian.net/wiki/x/eQBXnw

**最終更新:** May 9, 2025

---

## 📝 実装時の注意点

### ベストプラクティス

1. **バッチ処理**
   - 1回の通知メールで全契約処理
   - 効率的な一括操作

2. **エラーハンドリング**
   - ステップ1での必須項目チェック
   - 権限・オフィス検証

3. **データ整合性**
   - 共通設定の一致確認
   - 代表者名順序の保持

4. **ファイル管理**
   - 契約毎に1PDFの制限
   - multipart形式の正しい使用

---

*このガイドは Multiple Contracts API の基本的な使用方法を説明しています。詳細な仕様やエラーハンドリングについては、公式API仕様書を参照してください。*