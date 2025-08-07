# User can Create & Update multiple contracts [Step 1 & 2]

Jira Link: https://moneyforward.atlassian.net/browse/STL-6518?atlOrigin=eyJpIjoiMzgyZTEyZWJlYmE4NDU5ZGI5NjRiODJjNTczMWUzYTciLCJwIjoiaiJ9
Updated At: May 9, 2025

# Acceptance Criteria

### 📘 **User Journey: Multiple Contract Creation via API**

### **Step 1: Upload & Basic Setup**

- **Action**: User uploads contracts via API (minimum 2, maximum 10 contracts per request).
- **Validations**:
    - 
    - Must upload **at least 2 contracts**, max **10**.
    - Each contract must include:
        - PDF file (only one per contract)
        - Includes error cases
        - Application Type (mandatory)
- ❗ **Error Handling**:
    
    If any required field is missing, the system will prevent progressing to Step 2.
    
- **Common for Multi contracts**
    - Workflow Template
    - Partner Company (not representative name)
    - Contract Method (e-Contract only)
    - PIC User
    - Remarks / Comments (Memo)
- **Partner Selection & “Add More” Option** is provided for adding counterparty details.
- ✅ **Validation**:
    
    All contracts in the batch **must** have the same values for these shared settings. If any mismatch is found, an error is returned will not proceed to Step 2
    

---

### **Step 2: Workflow & Partner Selection**

**User Action:**

- Selects:
    - **Internal Workflow Template**
    - **Partner Company → R**epresentative Name

### ✅ **Out of Scope**

- **Step 3 (Stamp Setting)** is excluded from this batch API flow.
    
    → Existing contract stamp configuration will apply separately post-creation.
    

---

- **Step 4: Submit Contracts**
    - Review and Submit
    - Single click for entire contract

---

### 📝 **Important Notes**

- **📧 Email Notification**:
    
    Only **one notification email** is sent per batch, regardless of the number of contracts created.
    
- **📎 One Document Per Contract**:
    
    Each contract supports **only one PDF upload** at creation time.
    
- **📌 Common Batch Settings Include**:
    - Contract Type
    - Document Type
    - Partner Reminder Settings

Draft Note : 

- The user can create a multiple (1-10) contracts at once, which are to be in the sending box, per request.
    - Each contract can have different Contract Name, Application Template and Contract Type.
    - Partner Company, Workflow template and Contract Method (E-Contract) should be the same for all contracts.
    - The contracts are treated as a pack of contracts, and at the signature request process, to be treated as one batch. Hence the request notification email is to be send only one time for one batch.
- The user can get a single contract or the group of multiple contracts, which are in the sending box, per request.
- The user can edit a single contract or multiple contracts which is in the sending box, per request.
- The user can upload only one document for each contract at a time.

# API Flows

Ref: 

- Swagger link: [https://api.contract.moneyforward.com/v1/docs/index.html](https://api.contract.moneyforward.com/v1/docs/index.html)
- Confluence: [https://moneyforwardvietnam.atlassian.net/wiki/x/eQBXnw](https://moneyforwardvietnam.atlassian.net/wiki/x/eQBXnw)

## 1. Create multiple contracts (AC-Step 1):

- Description: user can create multiple contracts with common properties and separated information.
- API : `/v1/multiple_contracts [POST]`
- Request
    - Note :
        - **person_in_charge_id →**  `/v1/users [GET]`
        - **document_type_id →** `/v1/document_types [GET]`
        - **contract_type_id →** `/v1/contract_types [GET]`
        - **workflow_template_id →** `/v1/workflow_templates [GET]`
    
    ```json
    {
        "person_in_charge_id": "pic_id",
        "workflow_template_id": "workflow_template_id",
        "contracts": [
            {
                "name": "contract_1",
                "contract_type_id": "contract_type_id_1",
                "document_type_id": "document_type_id_1"
            },
            {
                "name": "contract_2",
                "contract_type_id": "contract_type_id_2",
                "document_type_id": "document_type_id_2"
            }
        ]
    }
    ```
    
- Response:
    - **Success** :
        
        ```json
        {
            "id": "multiple_contracts_id",
            "worfklow_template_id": "workflow_template_id",
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
        
    - **Error** :
        
        
        | If | Result |
        | --- | --- |
        | Number of Contracts <2 | 400 - Bad Request |
        | Number of Contract > 10 | 400 - Bad Request |
        | Incorrect Person In Charge Id | 400 - Bad Request |
        | Incorrect Workflow Template Id | 400 - Bad Request |
        | Any un-Expected Error | 500 - Internal Server Error |

## 2. Update multiple contracts (AC-Step 1):

- Description:
    - Users can update **existing** or **add new** contracts to the multiple contracts group.
    - Users DO NOT need to submit all contracts. They just need to submit the contract they want to update.
    - The common properties (e.g., person_in_charge_id) will be reflected to all contracts.
- API : `/v1/multiple_contracts/:id [PUT]`
    - Note :
        - **id** is from the response of ****`/v1/multiple_contracts [POST]`
- Request Body :

```json
{
    "person_in_charge_id": "pic_id",
    "workflow_template_id": "workflow_template_id",
    "contracts": [
        {
		        "id": "contract_1_id", // update existing contract
            "name": "contract_1",
            "contract_type_id": "contract_type_id_1",
            "document_type_id": "document_type_id_1"
        },
        {
			      "id": null, // add new contract
            "name": "contract_2",
            "contract_type_id": "contract_type_id_2",
            "document_type_id": "document_type_id_2"
        }
    ]
}
```

- Response :
    - Response is the same as GET/POST API but it returns the changed contracts only.
- Error : The error cases are the same as create contract with some extra below.

| If | Result |
| --- | --- |
| - contract is created in other office.
- user is not the applicant. | 404 - Contract not found |
| Any un-Expected Error | 500 - Internal Server Error |

## 3. Delete multiple contracts (AC - Any steps)

- Description: Users can and only can delete all contracts at once. After deletion, user cannot get this multiple contract group anymore.
- API: `/v1/multiple_contracts/:id [DELETE]`
- Response: 200 (even if the contract is already deleted).
- Error:
    
    
    | If | Result |
    | --- | --- |
    | - multiple contracts are created in other office.
    - user is not the applicant. | 404 - Contract not found |
    | Any un-Expected Error | 500 - Internal Server Error |

## 4. Get multiple contracts (AC - Any steps):

- Description: Users can review the contract information after operating any modification API.
- API : `/v1/multiple_contracts/:id [GET]`
    - Note :
        - **id** is from the response of this API - ****`/v1/multiple_contracts [POST]`
- Response :
    - **Success** :

```json
{
    "id": "multiple_contracts_id",
    "worfklow_template_id": "workflow_template_id",
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

- Error :

| If | Result |
| --- | --- |
| - multiple contracts are created in other office.
- user is NOT the applicant.
- multiple contracts are already deleted. | 404 - Contract not found |
| Any un-Expected Error | 500 - Internal Server Error |

## 5. Get individual contract in multiple contracts (AC - Any steps)

- Description: Users may want to get individual contract information to update that specific contract by other APIs.
- API: `/v1/multiple_contracts/:id/contracts/:id [GET]`
- Response:
    
    ```json
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
    ```
    
- Errors: the error case is the same as get multiple contracts detail.

## 6. Get list of multiple contracts (AC - Any steps)

- Description: Users can get a list of multiple contracts.
- API: `/v1/multiple_contracts [GET]`
- Response: same response as Get multiple contracts detail API but in a list format.

## 7. Upload document for individual contract (AC - Step 1)

- Description: Users can only upload document for each contract per API call.
- API: /v1/multiple_contracts/:id/contracts/:contract_id/documents [POST]
- Request:
    
    ```json
    multipart/form-data
    -- form 'file=@"main_contract.pdf"'
    ```
    
- Response: 200
- Errors: error cases are the same when upload document for single contract.

## 8. Save multiple contract fields (AC - Step 1)

- Description:
    - Users can edit contract fields of any existing contracts in the multiple contracts.
    - If users do not submit contract, that contract will remain unchanged.
- API: `/v1/multiple_contracts/:id/fields [PUT]`
- Request:
    
    ```json
    [
      {
        "id": "contract_1_id"
        "contract_fields": [
          {
            "id": "field_1_id",
            "name": "自動更新の有無",
            "value": {
              "data": "anything"
            }
          }
        ]
      },
      {
        "id": "contract_2_id"
        "contract_fields": [
          {
            "id": "field_2_id",
            "name": "自動更新の有無",
            "value": {
              "data": "anything"
            }
          }
        ]
      },
    ]
    ```
    
- Response: return field information of submitted contracts.
- Error: error cases are the same as edit contract fields of single contract API.

## 9. Save multiple contracts partner companies (AC - Step 2)

- Description:
    - As partner information (except representative name) is the same for all contracts, users can make one API call and information will be reflected.
    - The representative name users submit in this API request will reflect to all contracts and they can update individual contract if they want (by another API).
- API: `/v1/multiple_contracts/:id/partner_companies [POST]`
- Request:
    
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
      },
      {
        "name": "partner 2",
        "representative_name": "representative name 2",
        "approvers": [
          {
            "email": "partner@email.com",
            "name": "approver 1",
            "company_name": "partner 2",
            "access_key": "",
            "locale": "ja"
          }
        ]
      },
    ]
    ```
    
- Response
    
    ```json
    [
      {
        "id": "contract_1_id",
        "partner_companies": [
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
          },
          {
            "name": "partner 2",
            "representative_names": "representative name 2",
            "approvers": [
              {
                "email": "partner@email.com",
                "name": "approver 1",
                "company_name": "partner 2",
                "access_key": "",
                "locale": "ja"
              }
            ]
          },
        ]
      },
      {
        "id": "contract_2_id",
        "partner_companies": [
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
          },
          {
            "name": "partner 2",
            "representative_names": "representative name 2",
            "approvers": [
              {
                "email": "partner@email.com",
                "name": "approver 1",
                "company_name": "partner 2",
                "access_key": "",
                "locale": "ja"
              }
            ]
          },
        ]
      } 
    ]
    ```
    
    - Error: the error cases are the same with API to save partner companies for single contract.

## 10. Save partner representative name for individual contract (AC - Step 2)

- Description:
    - as mentioned in **9.**, users may want to edit the representative name for individual contract in the multiple contracts so they can use this API.
    - As one contract workflow can have many partners so users **must submit the representative name in correct order** (same order of partner companies which can be viewed by API returning individual contract information).
- API: `/v1/multiple_contracts/:id/contracts/:contract_id/partner_representative_names [PUT]`
- Request:
    
    ```json
    {
    	"representative_names": [
    		"repname_1", // partner 1
    		"repname_2", // partner 2
    	]
    }
    ```
    
- Response:
    
    ```json
    {
        "id": "{contract_id}",
        "partner_companies": [
          {
            "name": "partner 1",
            "representative_name": "repname_1",
          },
          {
            "name": "partner 2",
            "representative_names": "repname_2",
          },
        ]
      }
    ```
    
- Error:
    
    
    | If | Result |
    | --- | --- |
    | - Multiple contracts are not created in the same office
    - User is not applicant | 404 - not found |
    | - Number of representative names does not match number of partner companies, | 400 - bad request |
    | - Other errors | 500 - internal server error |