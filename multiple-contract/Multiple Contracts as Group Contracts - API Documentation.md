# Multiple Contracts as Group Contracts - API Documentation

## 📋 Overview
This document outlines the new API proposals for handling **multiple contracts** (group contracts) in MoneyForward Cloud Contract service. These APIs work alongside existing single contract APIs to provide comprehensive contract management capabilities.

## 🏗️ Structure Design

### Exposed Structure
- **Multiple contracts** (or group) where all contracts are at the same level
- Group information exposed to users via `multiple_contract_id` (first level field ID)
- Users can manage contracts in group by a single API with this ID

### Internal Structure
- **Database storage**: Main-child structure
- **First contract**: Main contract (stored in `contracts` table)
- **Other contracts**: Child contracts (stored in `child_contracts` table)  
- **Group ID**: Uses the ID of main contract as `multiple_contract_id`
- **Contract identifiers**: Each contract uses `id` field in custom format

### Response Format Example
```json
{
    "id": "multiple_contracts_id", // <contracts.id>
    "contracts": [
      {
        "id": "contract_1_id", // <M_contracts.id_contracts.id>
        "name": "contract 1"
      },
      {
        "id": "contract_2_id", // <C_contracts.id_child-contracts.id>
        "name": "contract 2"
      }
    ]
}
```

---

## 🚀 API Specifications

**Base URL:** `https://api.contract.moneyforward.com/v1/`

### 1. Get Multiple Contracts
**Endpoint:** `GET /v1/multiple_contracts/:id`

**Description:** Get multiple contracts by the `multiple_contract_id`. Response object for each contract is nearly identical to single contract response.

**Response:**
```json
{
    "id": "multiple_contracts_id",
    "workflow_template_id": "workflow_template_id",
    "person_in_charge_id": "pic_id",
    "contracts": [
      {
        "id": "contract_1_id",
        "number": "000001-1",
        "name": "contract 1",
        "document_type_id": "document_id",
        "contract_type_id": "contract_type_id",
        "type": "contract_type_value",
        "document": {
          "id": "document_id",
          "name": "contract labor",
          "file_type": "pdf",
          "path": ""
        },
        "contract_fields": [],
        "partner_companies": []
      },
      {
        "id": "contract_2_id",
        "number": "000001-2",
        "name": "contract 2",
        "document_type_id": "document_id",
        "contract_type_id": "contract_type_id",
        "type": "contract_type_value",
        "contract_fields": [],
        "partner_companies": []
      }
    ]
}
```

---

### 2. Get Single Contract in Multiple Contracts
**Endpoint:** `GET /v1/multiple_contracts/:id/contracts/:id`

**Description:** Get single contract in group by specific contract ID. Provides flexibility for updating contract information with small changes in the group.

**Response:**
```json
{
    "id": "contract_2_id",
    "number": "000001-2",
    "name": "contract 2",
    "document_type_id": "document_id",
    "contract_type_id": "contract_type_id",
    "workflow_template_id": "workflow_template_id",
    "person_in_charge": "pic_id",
    "contract_fields": [],
    "partner_companies": []
}
```

---

### 3. Get List of Multiple Contracts (Groups)
**Endpoint:** `GET /v1/multiple_contracts`

**Description:** Get list of multiple (groups) contracts. This API does not include single contracts in response or any deleted multiple contracts.

**Response:**
```json
[
  {
    "id": "multiple_contract_id",
    "workflow_template_id": "workflow_template_id",
    "person_in_charge": "pic_id",
    "contracts": []
  },
  {
    "id": "multiple_contract_id_2",
    "workflow_template_id": "workflow_template_id",
    "person_in_charge": "pic_id",
    "contracts": []
  }
]
```

---

### 4. Create Multiple Contracts
**Endpoint:** `POST /v1/multiple_contracts`

**Description:** Create multiple contracts by submitting all contracts information in one body request. Returns `multiple_contract_id` to manage contracts together.

**Requirements:**
- Users **must submit at least two contracts** in the request
- Can submit common properties (`person_in_charge_id`, `workflow_template_id`) and separated properties (`contract_type_id`, `document_type_id`)

**Request:**
```json
{
    "person_in_charge_id": "LJjWAKmP46M7RlpO7XoN10Yz",
    "workflow_template_id": "YkXpLQNzvMBa8GrcowJe5481",
    "contracts": [
        {
            "name": "contract_1",
            "contract_type_id": "contract_type_id_2",
            "document_type_id": "document_type_id_3"
        },
        {
            "name": "contract_2",
            "contract_type_id": "contract_type_id_3",
            "document_type_id": "document_type_id_3"
        }
    ]
}
```

**Response:** Same as GET API

---

### 5. Delete Multiple Contracts
**Endpoint:** `DELETE /v1/multiple_contracts/:id`

**Description:** Delete all multiple contracts at the same time. After deletion, GET API will return 404 error code.

**Response:** `200`

---

### 6. Update Multiple Contracts
**Endpoint:** `PUT /v1/multiple_contracts/:id`

**Description:** Update existing multiple contracts and add new contracts simultaneously. Payload is nearly the same as creating multiple contracts.

**Features:**
- Update existing contracts in group
- Add new contracts (if id is null or not exists)
- Only submitted contracts are updated

**Request:**
```json
{
    "person_in_charge_id": "LJjWAKmP46M7RlpO7XoN10Yz",
    "workflow_template_id": "YkXpLQNzvMBa8GrcowJe5481",
    "contracts": [
        {
            "id": "contract_1_id", // update contract 1
            "name": "contract_1",
            "contract_type_id": "contract_type_id_2",
            "document_type_id": "document_type_id_3"
        },
        {
            "id": null, // create new contract
            "name": "contract_3",
            "contract_type_id": "contract_type_id_2",
            "document_type_id": "document_type_id_3"
        }
    ]
}
```

**Response:** Same as GET API

---

### 7. Upload Multiple Contracts Document
**Endpoint:** `POST /v1/multiple_contracts/:id/contracts/:id/documents`

**Description:** Upload document for contracts in group. Currently accepts one file per contract per request to avoid unexpected response time.

**Request:**
```
multipart/form-data
--form 'file=@"/main_contract_document.pdf"'
```

**Response:** `200`

---

### 8. Save Multiple Contract Fields
**Endpoint:** `PUT /v1/multiple_contracts/:id/fields`

**Description:** Update multiple contract fields for contracts in group. Field information for each contract is identical to single contract's fields request.

**Note:** Only updates contract fields for submitted contracts.

---

### 9. Add Partner Companies to Multiple Contracts
**Endpoint:** `POST /v1/multiple_contracts/:id/partner_companies`

**Description:** Add partner companies to contracts in group.

---

### 10. Send Multiple Contracts for Approval
**Endpoint:** `POST /v1/multiple_contracts/:id/confirm`

**Description:** Send multiple contracts for approval process.

---

### 11. Remind Multiple Contracts
**Endpoint:** `POST /v1/multiple_contracts/:id/remind`

**Description:** Send reminder for multiple contracts.

---

### 12. Withdraw Multiple Contracts  
**Endpoint:** `POST /v1/multiple_contracts/:id/withdraw`

**Description:** Withdraw multiple contracts at the same time.

---

### 13. Download Multiple Contracts Certificate
**Endpoint:** `GET /v1/multiple_contracts/:id/certificate`

**Description:** Download certificate for multiple contracts.

---

## 🔄 Multiple Contract Setup Workflow

The typical workflow for setting up multiple contracts:

1. **Create multiple contracts:** `POST /v1/multiple_contracts`
2. **Get multiple contracts:** `GET /v1/multiple_contracts/:id`
3. **Get list of multiple contracts:** `GET /v1/multiple_contracts`
4. **Update multiple contracts:** `PUT /v1/multiple_contracts/:id`
5. **(Optional) Delete multiple contracts:** `DELETE /v1/multiple_contracts/:id`
6. **Upload document:** `PUT /v1/multiple_contracts/:id/contracts/:id/documents`
7. **Update contract fields:** `PUT /v1/multiple_contracts/:id/fields`
8. **Add partner companies:** `POST /v1/multiple_contracts/:id/partner_companies`
9. **Send for approval:** `POST /v1/multiple_contracts/:id/confirm`
10. **Withdraw if needed:** `POST /v1/multiple_contracts/:id/withdraw`

---

## ✅ Pros & Cons

### Pros
- **Unified API format:** Aligns with multiple contract format
- **Batch operations:** Operate on multiple contracts simultaneously with one API call
- **Same level hierarchy:** Accept payload of multiple contracts in same level hierarchy
- **Database optimization:** Compatible with existing database storage structure

### Cons
- **API complexity:** Additional API endpoints increase system complexity
- **Learning curve:** Developers need to understand both single and multiple contract APIs
- **Maintenance overhead:** More APIs to maintain and document

### Considerations
- **Backward compatibility:** Ensure existing single contract APIs continue to work
- **Performance impact:** Monitor response times with multiple contract operations
- **Error handling:** Implement comprehensive error handling for batch operations
- **Documentation:** Maintain clear documentation for both API sets

---

## 📝 Important Notes

- **Minimum requirement:** Must submit at least two contracts when creating multiple contracts
- **File upload limit:** Currently one file per contract per request
- **Database structure:** Uses main-child structure internally while exposing flat structure externally
- **ID format:** Custom ID format to handle main and child contract identification
- **Update behavior:** Only submitted contracts are updated in group operations

---

*This documentation covers the proposed API changes for multiple contracts functionality. Please refer to the main API documentation for single contract operations and existing functionality.*