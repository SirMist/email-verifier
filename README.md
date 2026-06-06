[Email Verifier](https://apify.com/gorang_m/email-verifier?fpr=data)

# Bulk Email Verifier - $1/1K | 99% Accuracy on Microsoft 365 Emails

Verify email lists in bulk before sending cold email campaigns. Tells you which emails are valid, invalid, disposable, role-based, or catch-all. Also detects the email provider (Microsoft 365, Google Workspace, Zoho, etc.) so you can plan your sending strategy.

Just paste your emails and run. No external accounts or API keys to set up.

## What You Get Per Email

| Field | Example | What It Tells You |
| --- | --- | --- |
| `result` | valid | Is this email deliverable? (valid / invalid / catch_all / unknown) |
| `quality` | good | Should you send to it? (good / risky / bad) |
| `is_disposable` | false | Is it a throwaway email? (Mailinator, Guerrilla, etc.) |
| `is_role_based` | false | Is it a department email? (info@, support@, sales@) |
| `is_free_provider` | false | Is it Gmail, Yahoo, Outlook? |
| `is_catch_all` | false | Does the domain accept everything? |
| `reason` | mailbox_exists | Why this result was given |
| `provider` | microsoft_365 | What email system the company uses |
| `mx_host` | acme.mail.protection.outlook.com | The actual mail server |

## Why the Provider Field Matters

Most verifiers just tell you valid/invalid. This actor also tells you **what email system the domain runs**: Microsoft 365, Google Workspace, Proofpoint, Mimecast, Zoho, ProtonMail, or custom.

Why that's useful:

- **Deliverability planning** -- Microsoft and Google handle cold email differently. Knowing the provider helps you adjust your sending.
- **Lead intelligence** -- a company on Proofpoint or Mimecast is likely enterprise. A company on Zoho is likely SMB.
- **Infrastructure insight** -- if you manage email domains for clients, knowing the MX host and provider saves you DNS lookups.

## How Verification Works

9 checks run in order. The actor stops at the first definitive answer:

1. **Syntax check** -- is the email formatted correctly?
2. **MX lookup** -- does the domain have a mail server?
3. **Disposable check** -- 5,000+ known throwaway domains
4. **Role-based check** -- department addresses (info@, support@, admin@, sales@)
5. **Free provider check** -- Gmail, Yahoo, Outlook, ProtonMail, etc.
6. **Microsoft 365 check** -- definitive valid/invalid via Microsoft's own API (97-99% accuracy). This is where most other verifiers return "unknown." We don't.
7. **Google Workspace check** -- detects Google-hosted domains, verifies via SMTP
8. **SMTP verification** -- direct mail server check for all other domains
9. **Catch-all detection** -- tests if the domain accepts any address

## Microsoft 365: Our Strongest Check

About 40-50% of B2B companies use Microsoft 365 for email. Standard SMTP verification doesn't work well on these domains because Microsoft blocks it. Most verifiers return "unknown" or "catch_all" for nearly half your business list.

This actor uses Microsoft's GetCredentialType API (the same check Microsoft's own login page uses) to give a definitive valid or invalid answer. 97-99% accuracy on the domains that matter most for B2B outreach.

## Input

```
{
    "emails": [
        "john@acme.com",
        "jane@startup.io",
        "test@mailinator.com",
        "support@company.com"
    ]
}
```

Or paste emails as comma/newline-separated text in the `emailsText` field. Supports up to 10,000 emails per run.

## Output Example

```
[
    {
        "email": "john@acme.com",
        "result": "valid",
        "quality": "good",
        "is_disposable": false,
        "is_role_based": false,
        "is_free_provider": false,
        "is_catch_all": false,
        "reason": "mailbox_exists",
        "provider": "microsoft_365",
        "mx_host": "acme-com.mail.protection.outlook.com"
    },
    {
        "email": "test@mailinator.com",
        "result": "valid",
        "quality": "bad",
        "is_disposable": true,
        "is_role_based": false,
        "is_free_provider": false,
        "is_catch_all": false,
        "reason": "disposable_domain",
        "provider": "custom",
        "mx_host": "mail.mailinator.com"
    },
    {
        "email": "support@company.com",
        "result": "valid",
        "quality": "risky",
        "is_disposable": false,
        "is_role_based": true,
        "is_free_provider": false,
        "is_catch_all": false,
        "reason": "valid_role_based",
        "provider": "google_workspace",
        "mx_host": "aspmx.l.google.com"
    }
]
```

Download results as CSV, JSON, or Excel from the Dataset tab.

## Pricing

$0.001 per email ($1 per 1,000). No subscription. No minimum.

| Emails | Cost |
| --- | --- |
| 1,000 | $1 |
| 5,000 | $5 |
| 10,000 | $10 |
| 50,000 | $50 |

## Two Modes

**Mode 1: Default (no setup)**
Leave SMTP fields empty. Uses Microsoft 365 API verification + syntax/MX/disposable/role checks. Covers ~40-50% of B2B emails with 97-99% accuracy. Other domains get flagged as unknown.

**Mode 2: Full SMTP (with your VPS)**
Add your SMTP verification server URL. Enables direct mail server checks for ALL domains. Set up [Reacher](https://github.com/reacherhq/check-if-email-exists) on a $3-5/month VPS with port 25 open.

## Speed

- Microsoft 365 domains: 500-1,000 emails/minute
- SMTP verification: 100-300 emails/minute
- Adjustable concurrency (1-20 parallel workers)

## FAQ

**How accurate is it?**
97-99% on Microsoft 365 domains (40-50% of B2B). 88-95% on other domains via SMTP. If you only use Mode 1 (no SMTP VPS), non-Microsoft domains will show as "unknown" -- still useful for filtering bad syntax, disposable, and role-based emails.

**What does quality "risky" mean?**
The email might be deliverable, but sending to it carries risk. Catch-all domains, role-based addresses, and unverifiable domains are flagged as risky. You decide whether to include them in your campaign.

**Can I use the results in Smartlead/Instantly?**
Yes. Export as CSV, filter by `quality = good`, and import the clean list.

**What about catch-all domains?**
Detected and flagged. About 15-25% of B2B domains accept all addresses. We flag these as `catch_all` with quality `risky` so you can decide.