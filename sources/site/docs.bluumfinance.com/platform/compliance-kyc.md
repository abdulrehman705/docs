# Source: https://docs.bluumfinance.com/platform/compliance-kyc

Every investment account must complete Know Your Customer (KYC) verification before trading. Bluum handles the compliance pipeline — you collect documents from the user and submit them through the API.

## 

[​](https://docs.bluumfinance.com/#how-kyc-works)

How KYC works

```
Upload Documents → Processing → Approved / Rejected
                       │
                  (auto-approved
                   in sandbox)
```

1. Create an account with identity and contact information
2. Upload identity documents (government-issued ID, proof of address)
3. Bluum verifies the documents against the identity information
4. Account is approved or rejected

In sandbox, documents are auto-approved. In production, verification typically completes within minutes but may take up to 24 hours for manual review.

## 

[​](https://docs.bluumfinance.com/#document-types)

Document types

| Type | Description | Required |
| --- | --- | --- |
| `id_verification` | Government-issued photo ID (passport, driver’s license, national ID) | Yes |
| `proof_of_address` | Utility bill, bank statement, or tax document showing current address | Situational |
| `w9_form` | W-9 tax form | Situational |

## 

[​](https://docs.bluumfinance.com/#upload-a-document)

Upload a document

```
curl -X POST "$BASE_URL/documents" \
  -H "Authorization: Basic $AUTH" \
  -F "account_id=$INVESTOR_ID" \
  -F "purpose=kyc" \
  -F "document_type=id_verification" \
  -F "file=@/path/to/drivers-license.jpg"
```

`account_id` sets the document’s owner to that investor; omit it to attach the document to the tenant directly. Use `user_id` to attach to a dashboard user instead. Supported file formats: JPEG, PNG, PDF. Maximum file size: 10MB.

## 

[​](https://docs.bluumfinance.com/#document-statuses)

Document statuses

| Status | Meaning |
| --- | --- |
| `processing` | Document uploaded, verification in progress |
| `approved` | Verification successful |
| `rejected` | Verification failed — upload a new document |

## 

[​](https://docs.bluumfinance.com/#checking-document-status)

Checking document status

Poll the document endpoint or use webhooks:

```
# Get a specific document
curl -X GET "$BASE_URL/documents/{document_id}" \
  -H "Authorization: Basic $AUTH"

# List all documents for an account
curl -X GET "$BASE_URL/documents?status=approved" \
  -H "Authorization: Basic $AUTH"
```

Use [webhooks](https://docs.bluumfinance.com/platform/webhooks) to receive real-time notifications when document verification completes, rather than polling.

## 

[​](https://docs.bluumfinance.com/#handling-rejections)

Handling rejections

When a document is rejected, the response includes a reason. Common rejection reasons:

| Reason | Resolution |
| --- | --- |
| Document is blurry or illegible | Re-upload with higher quality image |
| Document is expired | Upload a current, non-expired document |
| Name mismatch | Ensure the name on the document matches the account identity |
| Document type not accepted | Use a supported government-issued photo ID |

Prompt the user to upload a replacement document. There is no limit on upload attempts.

## 

[​](https://docs.bluumfinance.com/#compliance-workflow-statuses)

Compliance workflow statuses

For more complex compliance scenarios, Bluum uses a multi-step compliance workflow:

| Status | Meaning |
| --- | --- |
| `IN_PROGRESS` | Compliance checks are running |
| `PENDING_REVIEW` | Requires manual review |
| `APPROVED` | All checks passed |
| `REJECTED` | One or more checks failed |
| `SUSPENDED` | Account compliance suspended |
| `EXPIRED` | Compliance window expired, re-verification needed |

## 

[​](https://docs.bluumfinance.com/#key-endpoints)

Key endpoints

| Method | Path | Description |
| --- | --- | --- |
| `POST` | `/documents` | Upload a document (multipart) |
| `GET` | `/documents` | List documents (filter by `account_id`, `user_id`, `purpose`, `status`) |
| `GET` | `/documents/{document_id}` | Get document detail |
| `GET` | `/documents/{document_id}/download` | Download the document bytes |
| `DELETE` | `/documents/{document_id}` | Delete a document |

[Accounts](https://docs.bluumfinance.com/platform/accounts) [Wallets](https://docs.bluumfinance.com/platform/wallets)

⌘I