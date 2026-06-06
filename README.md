[Email Verifier](https://apify.com/amr-mando/email-verifier?fpr=data)

# Email Verifier 📧

Verify email addresses in bulk using advanced email verification. Get deliverability status, MX records, disposable detection, and more.

🎯 Free plan: Maximum 30 results per run 💡 We don't get paid for free users usage, that's why we have to sadly limit it.

**Upgrade to a paid Apify plan for unlimited email verification!**

## Features

- **Single or Bulk Verification**: Verify one email or thousands at once
- **CSV File Support**: Upload CSV files with automatic email column detection
- **Full CSV Preservation**: When uploading a CSV, ALL original columns are preserved in the output (name, company, phone, etc.)
- **Comprehensive Results**: Get detailed verification data including:

- Deliverability score (0-100)
- Result status (deliverable, risky, undeliverable, unknown)
- MX record validation
- Disposable email detection
- Free email provider detection
- Catch-all detection
- And more...

## Input

### Direct Email Input

Provide emails as a list in the `emails` field. You can enter one email per line.

### CSV File Upload

Upload a CSV file containing email addresses. The Actor will:

1. **Auto-detect the email column** (looks for columns named email, Email, e-mail, etc.)
2. **Preserve ALL original columns** in the output
3. **Merge verification results** with your original data

Supported auto-detect column names:

- `email`, `Email`, `EMAIL`
- `e-mail`, `E-mail`
- `emailAddress`, `email_address`
- `mail`, `Mail`
- And similar variations

You can also specify the exact column name using the `emailColumn` field.

## Output

### For Direct Email Input

Each verified email returns the exact API response:

```
{
  "email": "example@domain.com",
  "username": "example",
  "domain": "domain.com",
  "mx_record": "mx.domain.com",
  "provider": "provider_name",
  "score": 100,
  "isv_format": true,
  "isv_domain": true,
  "isv_mx": true,
  "isv_noblock": true,
  "isv_nocatchall": false,
  "isv_nogeneric": true,
  "is_disposable": false,
  "is_free": true,
  "result": "deliverable",
  "reason": "accepted_email"
}
```

### For CSV Input (Full Lead List)

When you upload a CSV, the output contains **ALL your original columns PLUS verification results**.

Example: If your CSV has columns `name, company, email, phone`, the output will be:

```
{
  "name": "John Doe",
  "company": "Acme Inc",
  "email": "john@acme.com",
  "phone": "+1234567890",
  "verification_email": "john@acme.com",
  "verification_username": "john",
  "verification_domain": "acme.com",
  "verification_mx_record": "mx.acme.com",
  "verification_provider": "google",
  "verification_score": 100,
  "verification_isv_format": true,
  "verification_isv_domain": true,
  "verification_isv_mx": true,
  "verification_isv_noblock": true,
  "verification_isv_nocatchall": true,
  "verification_isv_nogeneric": true,
  "verification_is_disposable": false,
  "verification_is_free": false,
  "verification_result": "deliverable",
  "verification_reason": "accepted_email"
}
```

**Note:** Verification fields are prefixed with `verification_` to avoid conflicts with your original columns.

### Result Values

- `deliverable`: Email is valid and can receive messages
- `risky`: Email exists but may have issues (catch-all, etc.)
- `undeliverable`: Email does not exist or cannot receive messages
- `unknown`: Verification could not be completed

## Usage Examples

### Example 1: Verify Single Email

```
{
  "emails": ["test@example.com"]
}
```

### Example 2: Verify Multiple Emails

```
{
  "emails": [
    "john@company.com",
    "jane@company.com",
    "info@company.com"
  ]
}
```

### Example 3: Upload Full Lead List CSV

Upload your CSV file with any columns you want to preserve:

```
{
  "csvFile": "https://api.apify.com/v2/key-value-stores/xxx/records/leads.csv",
  "emailColumn": "Contact Email"
}
```

Your output will contain all original columns (first_name, last_name, company, phone, etc.) plus the verification results!

## Rate Limits and Timing

The Actor automatically adjusts processing time based on the number of emails:

- Up to 250 emails: ~45 seconds
- 250-500 emails: ~45-55 seconds
- 500-1000 emails: ~55-100 seconds
- 1000-5000 emails: ~100-130 seconds
- 5000+ emails: ~130-180 seconds

## Support

For issues or questions, please contact the Actor developer.