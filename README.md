[Email Verifier](https://apify.com/juryless_lens/email-verifier?fpr=data)

# Email Verifier — Bulk Email Validation

Verify email addresses in bulk with comprehensive validation. Checks syntax correctness, DNS MX records, disposable/temporary email providers, and identifies free email services. Clean your email lists before sending campaigns.

## What does it return?

| Field | Description |
| --- | --- |
| `email` | The email address checked |
| `valid` | Overall validity (true/false) |
| `reason` | Why it passed or failed |
| `domain` | Email domain |
| `isDisposable` | Whether it's a known temporary email provider |
| `isFreeProvider` | Whether it's a free email (Gmail, Yahoo, etc.) |
| `hasMxRecord` | Whether the domain has valid mail servers |
| `mxRecords` | List of MX records with priorities |

## Input

```
{
    "emails": [
        "john@gmail.com",
        "fake@mailinator.com",
        "bad@@@invalid",
        "ceo@apple.com"
    ],
    "checkMx": true,
    "checkDisposable": true
}
```

## Use Cases

- **Email Marketing**: Clean your mailing lists before campaigns to reduce bounce rates
- **Lead Qualification**: Filter out disposable emails from lead gen forms
- **CRM Hygiene**: Periodically validate contacts in your sales pipeline
- **Registration Screening**: Prevent fake signups with disposable email detection