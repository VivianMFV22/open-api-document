# MoneyForward Cloud Contract API - Slides

---

## Slide 1: API Overview
### マネーフォワード クラウド契約 API

**Base URL:** `https://api.contract.moneyforward.com/v1/`

**17 APIs Available:**
- 📊 Status & Info APIs (5)
- 🔨 Contract Creation APIs (2)
- ⚙️ Contract Management APIs (5)
- 🚀 Contract Operations APIs (5)

**2 Main Use Cases:**
- 通常契約 (Normal Contract)
- テンプレート契約 (Template Contract)

---

## Slide 2: API Categories Overview

| Category | Normal Contract | Template Contract | Both |
|----------|:---------------:|:-----------------:|:----:|
| **Info & Status** | 3 APIs | 1 API | 6 APIs |
| **Creation** | 1 API | 1 API | - |
| **Restrictions** | Full Access | Limited | Shared |

**Key Difference:**
- Normal: Full flexibility, manual setup
- Template: Pre-configured, automated workflow

---

## Slide 3: Authentication & Prerequisites

### 🔑 Required Headers
```
Authorization: Bearer ${アクセストークン}
x-email: ${ユーザーのメールアドレス}
x-token: ${アクセストークン}
```

### 📋 Before You Start
1. Get access token from MoneyForward
2. Verify user permissions
3. Choose contract type (Normal vs Template)
4. Prepare contract information

---

## Slide 4: Case 1 - Normal Contract Workflow

### Step-by-Step Process

**Step 1: Information Gathering** (4 APIs)
- `GET /contract_types` ✅
- `GET /workflow_templates` ✅  
- `GET /document_types` ✅ (Optional)
- `GET /users` ✅

**Step 2: Contract Creation**
- `POST /contracts` ✅

---

## Slide 5: Case 1 - Normal Contract Setup

**Step 3: Contract Configuration** (3 APIs)
- `POST /contracts/{ID}/documents` ✅
- `POST /contracts/{ID}/partner_companies` ✅
- `PUT /contracts/{ID}/fields` ✅

**Step 4: Send for Approval**
- `POST /contracts/{ID}/confirm` ✅

**Step 5: Management** (3 APIs)
- `POST /contracts/{ID}/remind` ✅
- `POST /contracts/{ID}/withdraw` ✅
- `GET /contracts/{ID}/certificate` ✅

---

## Slide 6: Case 2 - Template Contract Workflow

### Streamlined Process

**Step 1: Information Gathering** (3 APIs)
- `GET /workflow_templates` ✅
- `GET /contract_templates` ✅ (Under Development)
- `GET /users` ✅

**Step 2: Template-based Creation**
- `POST /contracts/with_template` ✅

**Key Requirement:** Match workflow seal requirements with template seal settings

---

## Slide 7: Case 2 - Template Contract Setup

**Step 3: Contract Configuration** (2 APIs)
- `POST /contracts/{ID}/partner_companies` ✅
- `PUT /contracts/{ID}/fields` ✅

**Step 4: Send for Approval**
- `POST /contracts/{ID}/confirm` ✅

**Step 5: Management** (Same as Normal)
- `POST /contracts/{ID}/remind` ✅
- `POST /contracts/{ID}/withdraw` ✅
- `GET /contracts/{ID}/certificate` ✅

---

## Slide 8: Template Contract Features

### ✅ Advantages
- **Pre-configured:** Custom fields preserved
- **Automated:** Seal placements handled automatically
- **Consistent:** Standardized contract format
- **Efficient:** Fewer setup steps required

### 🎯 Perfect For
- Recurring contract types
- Standardized business processes
- Large-scale contract operations
- Compliance requirements

---

## Slide 9: Important Restrictions

### 🚫 Template Contract Limitations

**Cannot Upload Files:**
- ❌ `POST /contracts/{ID}/documents`
- ✅ Use template-configured documents

**Cannot Update Core Settings:**
- ❌ `PUT /contracts/{ID}` (workflow/contract type)
- ✅ Create new contract with `POST /contracts/with_template`

**Partner Count Constraint:**
- Must match template's partner company count

---

## Slide 10: Status & Monitoring APIs

### 📊 Available for Both Types

**Contract Listing:**
- `GET /contracts` - All draft contracts

**Contract Details:**
- `GET /contracts/{ID}` - Specific contract info

**Contract Updates:** (Normal Only)
- `PUT /contracts/{ID}` - Update draft contract

**Use Cases:**
- Progress monitoring
- Status verification
- Batch operations

---

## Slide 11: API Usage Patterns

### 🔄 Common Workflows

**Information → Creation → Configuration → Approval**

**Normal Contract:** `4 → 1 → 3 → 1 = 9 API calls`
**Template Contract:** `3 → 1 → 2 → 1 = 7 API calls`

### 📈 Efficiency Comparison
- Template contracts: **22% fewer API calls**
- Reduced configuration complexity
- Faster time-to-approval

---

## Slide 12: Error Handling & Validation

### ⚠️ Common Failure Points

**Step 4 (Confirm) Requirements:**
- All required information must be complete
- Missing data = API error response

**Template Specific:**
- Workflow-template compatibility
- Partner count validation
- File restrictions

**Best Practice:** Validate before `POST /contracts/{ID}/confirm`

---

## Slide 13: API Reference Summary

### 📚 Complete API List (17 Total)

**Information APIs (5):**
- Contract types, workflows, documents, users, templates

**Creation APIs (2):**
- Normal creation, template creation

**Configuration APIs (5):**
- Updates, documents, partners, fields, listing

**Operations APIs (5):**
- Confirm, remind, withdraw, certificate, details

**Documentation:** https://api.contract.moneyforward.com/v1/docs/index.html

---

## Slide 14: Implementation Checklist

### ✅ Before Implementation

**Technical Setup:**
- [ ] Access token obtained
- [ ] Authentication headers configured
- [ ] Base URL configured
- [ ] Error handling implemented

**Business Logic:**
- [ ] Contract type determined
- [ ] Workflow requirements defined
- [ ] Template needs assessed
- [ ] Partner information prepared

---

## Slide 15: Best Practices

### 🎯 Recommendations

**For Normal Contracts:**
- Use when flexibility is needed
- Ideal for unique contract types
- Allow custom document uploads

**For Template Contracts:**
- Use for standardized processes
- Leverage pre-configured settings
- Ensure workflow compatibility

**General:**
- Validate all data before confirmation
- Implement proper error handling
- Monitor API response times

---

## Slide 16: Next Steps

### 🚀 Getting Started

1. **Choose Your Path:**
   - Normal Contract: Maximum flexibility
   - Template Contract: Streamlined efficiency

2. **Implement Authentication:**
   - Secure token management
   - Header configuration

3. **Start with Information APIs:**
   - Understand available options
   - Plan your workflow

4. **Test with Draft Contracts:**
   - Validate integration
   - Verify all steps work

**Support:** Contact MoneyForward for technical assistance

---

## Slide 17: Q&A

### 💡 Common Questions

**Q: When should I use templates vs normal contracts?**
A: Templates for standardized, recurring contracts. Normal for custom requirements.

**Q: Can I modify template contracts after creation?**
A: Limited modifications. Cannot change core workflow or document settings.

**Q: What happens if confirmation fails?**
A: Check required fields completion. API returns specific error details.

**Q: Are there rate limits?**
A: Refer to official documentation for current API limits.

---

**Thank you for using MoneyForward Cloud Contract API!**

*For detailed documentation: https://api.contract.moneyforward.com/v1/docs/index.html*