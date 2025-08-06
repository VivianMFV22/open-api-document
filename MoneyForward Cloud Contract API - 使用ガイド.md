# マネーフォワード クラウド契約 API - 使用ガイド

## 📚 API一覧

| # | メソッド | エンドポイント | 説明 | 通常契約 | テンプレート契約 |
|---|--------|----------|-----|:-------:|:------------:|
| 1 | **GET** | `/contract_types` | 契約種別一覧の取得 | ✅ | ❌ |
| 2 | **GET** | `/workflow_templates` | ワークフロー一覧の取得 | ✅ | ✅ |
| 3 | **GET** | `/document_types` | 書類種別一覧の取得 (任意) | ✅ | ❌ |
| 4 | **GET** | `/users` | ユーザー一覧の取得 | ✅ | ✅ |
| 5 | **GET** | `/contract_templates` | テンプレート一覧の取得 (実装中) | ❌ | ✅ |
| 6 | **POST** | `/contracts` | 下書き契約の作成 | ✅ | ❌ |
| 7 | **POST** | `/contracts/with_template` | テンプレートで下書き契約の作成 (実装中) | ❌ | ✅ |
| 8 | **PUT** | `/contracts/{ID}` | 下書き契約の更新 | ✅ | ❌ |
| 9 | **GET** | `/contracts` | 下書き契約一覧の取得 | ✅ | ✅ |
| 10 | **GET** | `/contracts/{ID}` | 下書き契約詳細の取得 | ✅ | ✅ |
| 11 | **POST** | `/contracts/{ID}/documents` | ファイルの追加 | ✅ | ❌ |
| 12 | **POST** | `/contracts/{ID}/partner_companies` | 宛先情報の追加 | ✅ | ✅ |
| 13 | **PUT** | `/contracts/{ID}/fields` | 契約情報の追加 | ✅ | ✅ |
| 14 | **POST** | `/contracts/{ID}/confirm` | 書類の送信 | ✅ | ✅ |
| 15 | **POST** | `/contracts/{ID}/remind` | 署名依頼メールのリマインド送信 | ✅ | ✅ |
| 16 | **POST** | `/contracts/{ID}/withdraw` | 署名依頼メールの取下げ | ✅ | ✅ |
| 17 | **GET** | `/contracts/{ID}/certificate` | 合意締結証明書の取得 | ✅ | ✅ |

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

---

## 📋 ケース1: 通常の契約作成

### ステップ1: 必要な基本情報を取得

#### 1.1 契約種別一覧の取得
**`GET /contract_types`**

#### 1.2 ワークフロー一覧の取得
**`GET /workflow_templates`**
> **注意:** 「申請者が入力」設定を持つワークフローは選択できません

#### 1.3 書類種別一覧の取得 (任意)
**`GET /document_types`**

#### 1.4 ユーザー一覧の取得
**`GET /users`**

### ステップ2: 下書き契約の作成

**`POST /contracts`**

**必須パラメータ:**
- `contract_type_id`: ステップ1.1で取得したID
- `workflow_template_id`: ステップ1.2で取得したID
- `person_in_charge_id`: ステップ1.4で取得したID
- `document_type_id`: ステップ1.3で取得したID (任意)
- `name`: 契約名

### ステップ3: 契約情報の設定

#### 3.1 ファイルの追加
**`POST /contracts/{CONTRACT_ID}/documents`**

#### 3.2 宛先情報の追加
**`POST /contracts/{CONTRACT_ID}/partner_companies`**
> **注意:** 複数の相手先を追加可能

#### 3.3 契約情報の追加
**`PUT /contracts/{CONTRACT_ID}/fields`**

### ステップ4: 書類の送信

**`POST /contracts/{CONTRACT_ID}/confirm`**
> **重要:** ステップ1〜3の情報が過不足なく設定されている場合にリクエスト成功。不足があればエラーが返ります。

### ステップ5: 追加操作 (任意)

#### 5.1 署名依頼メールのリマインド送信
**`POST /contracts/{CONTRACT_ID}/remind`**

#### 5.2 署名依頼メールの取下げ
**`POST /contracts/{CONTRACT_ID}/withdraw`**

#### 5.3 合意締結証明書の取得
**`GET /contracts/{CONTRACT_ID}/certificate`**

---

## 🎨 ケース2: テンプレートを使用した契約作成

### ステップ1: 必要な基本情報を取得

#### 1.1 ワークフロー一覧の取得
**`GET /workflow_templates`**
> **注意:** 押印者を含むか含まないかという情報をワークフローごとに返却します

#### 1.2 テンプレート一覧の取得 (実装中)
**`GET /contract_templates`**

**テンプレート情報に含まれるもの:**
- 申請者が入力するカスタム項目がないテンプレート
- 自社の押印を含むか含まないかの情報
- 相手方の数の情報

#### 1.3 ユーザー一覧の取得
**`GET /users`**

### ステップ2: テンプレートで下書き契約の作成

**`POST /contracts/with_template`**

**必須パラメータ:**
- `workflow_template_id`: ステップ1.1で取得したID
- `contract_template_id`: ステップ1.2で取得したID  
- `person_in_charge_id`: ステップ1.3で取得したID
- `name`: 契約名

**重要な組み合わせ条件:**
- 押印者を含む/含まないワークフローと自社の押印を含む/含まないテンプレートを適切に組み合わせる必要があります

### ステップ3: 契約情報の設定

#### 3.1 宛先情報の追加
**`POST /contracts/{CONTRACT_ID}/partner_companies`**
> **注意:** テンプレートに設定されている相手方の数を合わせる必要があります

#### 3.2 契約情報の追加
**`PUT /contracts/{CONTRACT_ID}/fields`**

**テンプレート使用時の特徴:**
- テンプレートで設定したカスタム項目を保持したまま送付
- 自社と相手方のカスタム項目（テキストボックス/押印欄/チェックボックス）は承認者が通常通り画面上で記入可能

### ステップ4: 書類の送信

**`POST /contracts/{CONTRACT_ID}/confirm`**

### ステップ5: 追加操作 (任意)

テンプレート使用時も、通常契約と同様の追加操作が可能:
- **`POST /contracts/{CONTRACT_ID}/remind`** - 署名依頼メールのリマインド送信
- **`POST /contracts/{CONTRACT_ID}/withdraw`** - 署名依頼メールの取下げ  
- **`GET /contracts/{CONTRACT_ID}/certificate`** - 合意締結証明書の取得

---

## ⚠️ 重要な制限事項

### テンプレート使用時の制限

1. **ファイルの追加制限**
   - テンプレートで作成した契約の場合、`POST /contracts/{ID}/documents` で契約書を追加できません
   - テンプレートに設定した契約書を利用してください

2. **更新制限**
   - テンプレートで作成した契約の場合、`PUT /contracts/{ID}` でワークフローと契約種別を更新できません
   - ワークフローと契約種別を更新したい場合は、`POST /contracts/with_template` で新しい契約を作成してください

3. **相手方の数**
   - テンプレートに設定されている相手方の数を合わせる必要があります

---

## 📊 状態確認用API

### 契約一覧の取得
**`GET /contracts`**

### 契約詳細の取得
**`GET /contracts/{CONTRACT_ID}`**

---

## 🔄 契約更新 (通常契約のみ)

### 下書き契約の更新
**`PUT /contracts/{CONTRACT_ID}`**

**パラメータ:**
- `name`: 更新された契約名
- `contract_type_id`: 新しい契約種別ID

> **注意:** テンプレートで作成した契約では使用できません

---

## 📚 API仕様書

**完全なAPI仕様:** https://api.contract.moneyforward.com/v1/docs/index.html

**最終更新:** 2025/07/22

---

*このガイドは マネーフォワード クラウド契約 API の基本的な使用方法を説明しています。詳細な仕様やエラーハンドリングについては、公式API仕様書を参照してください。*