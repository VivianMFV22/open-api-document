# MoneyForward Cloud Contract API Manual

## 📋 Overview
This repository contains the API documentation for MoneyForward Cloud Contract service, specifically focusing on slides 10-13 which cover the core API specifications and implementation details.

## 📁 Files

### 1. API Manual (Text Format)
- **File:** `【開発者向け】マネーフォワード クラウド契約 API利用マニュアル.txt`
- **Description:** Original Japanese API manual in text format
- **Content:** Raw documentation extracted from slides 10-13

### 2. API Manual (Markdown Format)
- **File:** `【開発者向け】マネーフォワード クラウド契約 API利用マニュアル.md`
- **Description:** Formatted version with proper markdown structure
- **Content:** Well-organized documentation with headers, code blocks, and numbered lists

## 🚀 What's Included

### Slide 10: API Calling Example
- Practical example of calling MoneyForward Cloud Contract API
- Contract type retrieval API demonstration
- Complete cURL command examples with authentication headers

### Slide 11: API Specifications ① 
**Last Updated: 2025/07/22**

15 core API endpoints including:
- Contract type management (`GET /contract_types`)
- Workflow templates (`GET /workflow_templates`) 
- Document types (`GET /document_types`)
- User management (`GET /users`)
- Contract creation and management (`POST /contracts`, `PUT /contracts/{ID}`)
- File uploads (`POST /contracts/{ID}/documents`)
- Partner company management (`POST /contracts/{ID}/partner_companies`)
- Contract field updates (`PUT /contracts/{ID}/fields`)
- Document sending (`POST /contracts/{ID}/confirm`)
- Email reminders and withdrawals
- Certificate retrieval (`GET /contracts/{ID}/certificate`)

### Slide 12: API Specifications ②
**Last Updated: 2025/07/22**

Template-related APIs (under development):
- Template listing (`GET /contract_templates`)
- Template-based contract creation (`POST /contracts/with_template`)

### Slide 13: API Specifications ③
**Last Updated: 2025/07/22**

Detailed specifications for template APIs with comprehensive parameter explanations and usage guidelines.

## 🔗 API Reference
**Base URL:** `https://api.contract.moneyforward.com/v1/`  
**Full API Documentation:** https://api.contract.moneyforward.com/v1/docs/index.html

## 🔐 Authentication
All API calls require:
- `Authorization: Bearer {access_token}`
- `x-email: {user_email_address}`  
- `x-token: {access_token}`

## 📝 Usage Example
```bash
curl --location --request GET 'https://api.contract.moneyforward.com/v1/contract_types' \
--header 'Authorization: Bearer ${アクセストークン}' \
--header 'x-email: ${ユーザーのメールアドレス}' \
--header 'x-token: ${アクセストークン}'
```

## 🏷️ Version Information
- **Last Updated:** July 22, 2025
- **API Version:** v1
- **Content Source:** MoneyForward Cloud Contract Developer Manual Slides 10-13

## 📋 Notes
- Template-related APIs (endpoints 16-17) are currently under development
- Some template functionalities have restrictions on workflow and contract type updates
- Multiple partner companies can be added for contracts using templates

## 🤝 Contributing
This documentation is extracted from official MoneyForward materials. For API updates or clarifications, please contact MoneyForward support.

---
*This repository contains documentation materials for development purposes. Please refer to the official MoneyForward documentation for the most current information.*