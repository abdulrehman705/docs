# Source: https://docs.bluumfinance.com/guides/self-directed/kyc-verification

After creating an account, the user must complete KYC verification by uploading identity documents. This guide covers the upload flow, status tracking, and handling rejections.

## 

[​](https://docs.bluumfinance.com/#upload-flow)

Upload flow

### 

[​](https://docs.bluumfinance.com/#1-upload-a-document)

1\. Upload a document

```
curl -X POST "$BASE_URL/documents" \
  -H "Authorization: Basic $AUTH" \
  -F "account_id=$INVESTOR_ID" \
  -F "purpose=kyc" \
  -F "document_type=id_verification" \
  -F "file=@/path/to/id-document.jpg"
```

**Accepted formats:** JPEG, PNG, PDF (max 10MB) **Document types:**

- `id_verification` — Government-issued photo ID (required)
- `proof_of_address` — Utility bill, bank statement (if requested)
- `tax_document` — W-9 or equivalent (if requested)

### 

[​](https://docs.bluumfinance.com/#2-track-verification-status)

2\. Track verification status

**Option A: Poll**

```
curl -X GET "$BASE_URL/documents/$DOCUMENT_ID" \
  -H "Authorization: Basic $AUTH"
```

**Option B: Webhooks (recommended)** Subscribe to `document.approved` and `document.rejected` events:

```
curl -X POST "$BASE_URL/webhooks" \
  -H "Authorization: Basic $AUTH" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://your-app.com/webhooks/bluum",
    "events": ["document.approved", "document.rejected"]
  }'
```

### 

[​](https://docs.bluumfinance.com/#3-handle-the-result)

3\. Handle the result

| Status | What to do |
| --- | --- |
| `approved` | Proceed to bank linking and funding |
| `rejected` | Show the rejection reason and prompt re-upload |

## 

[​](https://docs.bluumfinance.com/#handling-rejections)

Handling rejections

When a document is rejected, the response includes a reason:

```
{
  "document_id": "doc_a1b2c3d4e5f6g7h8",
  "upload_status": "rejected",
  "rejection_reason": "Document is expired"
}
```

Display the reason to your user and allow them to upload a replacement. There is no limit on upload attempts. **Common rejection reasons:**

- Document is blurry or illegible
- Document is expired
- Name doesn’t match account identity
- Document type not recognized

## 

[​](https://docs.bluumfinance.com/#listing-documents)

Listing documents

```
# All documents for an account
curl -X GET "$BASE_URL/documents" \
  -H "Authorization: Basic $AUTH"

# Filter by status
curl -X GET "$BASE_URL/documents?status=approved" \
  -H "Authorization: Basic $AUTH"
```

## 

[​](https://docs.bluumfinance.com/#sandbox-behavior)

Sandbox behavior

In sandbox, all document uploads are auto-approved within seconds. To test rejection flows, use specific test values documented in your dashboard.

## 

[​](https://docs.bluumfinance.com/#next-steps)

Next steps

Once KYC is approved:

- [Link a bank account](https://docs.bluumfinance.com/guides/self-directed/bank-linking)
- [Make a deposit](https://docs.bluumfinance.com/guides/self-directed/deposits-withdrawals)

[Account Onboarding](https://docs.bluumfinance.com/guides/self-directed/account-onboarding) [Link a Bank Account](https://docs.bluumfinance.com/guides/self-directed/bank-linking)

⌘I