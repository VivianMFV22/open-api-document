# 2.   The user can confirm/send contracts, withdraw contracts and download certificates [Step 4]

Jira Link: https://moneyforward.atlassian.net/browse/STL-6519?atlOrigin=eyJpIjoiYTIzYjMzNzM5YTA4NDVkOGJkNjNmYWVmYWY1NjcxYWIiLCJwIjoiaiJ9
Updated At: May 9, 2025

# AC

- The user can get multiple certification for concluded contracts at once per request.
- The user can download a certification for a single concluded contract per request.
- The user can send a signature requests, which contains 10 contracts as maximum,  to the partner company at once per request.
- The user can send a single reminder for signature request, which contains 10 contracts as maximum,  to the partner company per request.
- The user can withdraw signature request, which contains 10 contracts as maximum, per request.

Out of Scope

- Remand and Reject flow (Existing API is not support) so we are avoiding this flow

# API Flows

Ref: 

- Swagger link: [https://api.contract.moneyforward.com/v1/docs/index.html](https://api.contract.moneyforward.com/v1/docs/index.html)
- Confluence: [https://moneyforwardvietnam.atlassian.net/wiki/x/eQBXnw](https://moneyforwardvietnam.atlassian.net/wiki/x/eQBXnw)

## 1. Send multiple contracts for approval

- Description:
    - Users can send all multiple contracts for approval.
    - The system will ensures all the required properties information are fulfilled.
- API: `/v1/multiple_contracts/:id/confirm [POST]`
- Response: 200
- Error: error cases are the same as send single contract API.

## 2. Remind multiple contracts

- Description: Users can remind multiple contracts at once.
- API: `/v1/multiple_contracts/:id/remind [POST]`
- Response: 200
- Error: error cases are the same as remind single contract API.

## 3. Withdraw multiple contracts

- Description: Users can withdraw multiple contracts at once.
- API: `/v1/multiple_contracts/:id/withdraw [POST]`
- Request:
    
    ```json
    {
    	"comment": "hello"
    }
    ```
    
- Response: 200
- Error: error cases are the same as withdraw single contract API.

## 4. Download multiple contracts certificate

- Description:
    - After concluding multiple contracts, user can download all or specific contract certificates.
    - If user submit the API call with query param contract_id, system will download individual file. Otherwise, system will download all files into 1 zip file.
- API: `/v1/multiple_contracts/:id/certificates [GET]`
- Query param:
    - contract_id: id of specific contract
- Response:
    - Single file: binary in PDF format.
    - All files: binary in Zip format - folder structure:
        - <main-contracts.name> (他<total-contracts>件)/
            - <contracts_1.name>/
                - certificate.pdf
            - <contracts_2.name>/
                - certificate.pdf
    - **Zip file name** is base64 encoded. So client side can decode from their end to get the exact zipfile name.
- Error: error cases are the same as downloading certificate for single contract API.