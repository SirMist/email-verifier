[Email Verifier](https://apify.com/pro100chok/email-verifier?fpr=data)

# Email Verifier — Bulk Email Validation, Deliverability & Risk Score

> Validate, clean, and score email addresses in bulk. Catch bounces before you send. Detect disposable, role-based, catch-all, and spam-trap addresses. Output is ready for CRMs, ESPs, and spreadsheet imports.

## What this Actor does

Run an array of email addresses through the actor and get back a clean, enriched verification record for each. Typical use-cases:

- **Clean your mailing list** before an ESP campaign — drop the dead addresses so your bounce rate stays low and your sender reputation stays high.
- **Validate leads** from a form / chat / landing page — flag fakes, typos, disposable addresses, and spam traps before they hit your CRM.
- **Score prospects** for cold-email outreach — separate high-confidence deliverable mailboxes from catch-all / role / unknown ones.
- **Cross-check** bought or scraped lists — detect gibberish, plus-tag aliases, and free-mail vs. business addresses.

No SMTP setup on your side, no API keys, no captcha, no proxies — just paste the list and run.

---

## Input format

```
{
  "emails": [
    "alex.morozov77@gmail.com",
    "jennifer.lee@icloud.com",
    "peter-anderson@hotmail.com"
  ],
  "concurrency": 3
}
```

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `emails` | `string[]` | ✅ | List of email addresses to verify. **Up to 50 per run.** |
| `concurrency` | `integer` | — | Parallel verifications (1–20, default `5`). Higher = faster, but more likely to hit upstream rate limits. |

---

## Output format

One flat record per email, pushed to the default dataset. Example (JSON):

```
{
  "address": "james_wilson87@hotmail.com",
  "status": "valid",
  "sub_status": "",
  "reason": "Mailbox exists and accepts mail.",
  "is_deliverable": true,
  "is_valid_format": true,
  "is_free_email": true,
  "is_business_email": false,
  "is_role_account": false,
  "is_disposable": false,
  "is_catch_all": false,
  "is_toxic": false,
  "is_spamtrap": false,
  "is_abuse": false,
  "is_global_suppression": false,
  "is_mailbox_full": false,
  "is_greylisted": false,
  "is_alias": false,
  "is_possible_typo": false,
  "has_plus_tag": false,
  "is_gibberish": false,
  "did_you_mean": null,
  "normalized_email": "james_wilson87@hotmail.com",
  "quality_score": 95,
  "deliverability_score": 95,
  "risk": "low",
  "account": "james_wilson87",
  "domain": "hotmail.com",
  "domain_age_days": 10983,
  "smtp_provider": "microsoft",
  "mx_found": true,
  "mx_record": "hotmail-com.olc.protection.outlook.com",
  "mx_records": ["hotmail-com.olc.protection.outlook.com"],
  "firstname": null,
  "lastname": null,
  "gender": null,
  "country": null,
  "region": null,
  "city": null,
  "zipcode": null,
  "processed_at": "2026-04-21 18:48:06.886",
  "verified_at": "2026-04-21 18:48:06.886"
}
```

### Field reference

**Status**

| Field | Type | Description |
| --- | --- | --- |
| `address` | string | The email that was verified |
| `status` | string | `valid` / `invalid` / `catch-all` / `unknown` / `spamtrap` / `abuse` / `do_not_mail` |
| `sub_status` | string | Granular reason code: `mailbox_not_found`, `disposable`, `role_based`, `possible_typo`, `greylisted`, etc. |
| `reason` | string | Plain-English explanation of the status |

**Derived verdicts** (computed on top of raw status)

| Field | Type | Description |
| --- | --- | --- |
| `is_deliverable` | boolean | Mailbox accepts mail (`status == valid`) |
| `is_valid_format` | boolean | Passes RFC-style syntax check |
| `is_free_email` | boolean | Free provider (Gmail, Yahoo, Outlook, Hotmail, iCloud, mail.ru, …) |
| `is_business_email` | boolean | Inverse of `is_free_email` |
| `is_role_account` | boolean | Shared mailbox (`info@`, `support@`, `sales@`, `hr@`, …) |
| `is_disposable` | boolean | Temporary / throwaway address |
| `is_catch_all` | boolean | Domain accepts any address — existence can't be confirmed |
| `is_toxic` | boolean | Known complainer / trap |
| `is_spamtrap` | boolean | Hard spam trap — never send |
| `is_abuse` | boolean | Known abuse / complaint address |
| `is_global_suppression` | boolean | On a global suppression list |
| `is_mailbox_full` | boolean | Mailbox quota exceeded |
| `is_greylisted` | boolean | Temporary SMTP reject |
| `is_alias` | boolean | Alias that forwards elsewhere |
| `is_possible_typo` | boolean | Looks like a typo — see `did_you_mean` |
| `has_plus_tag` | boolean | Gmail-style `local+tag@domain` aliasing |
| `is_gibberish` | boolean | Random-looking local part (e.g. `asdqwer123`) |

**Scoring**

| Field | Type | Description |
| --- | --- | --- |
| `quality_score` | integer | 0–100 deliverability score |
| `deliverability_score` | integer | Alias of `quality_score` |
| `risk` | string | `low` / `medium` / `high` / `unknown` |

**Metadata & enrichment**

| Field | Type | Description |
| --- | --- | --- |
| `did_you_mean` | string | Suggested fix if the address looks like a typo |
| `normalized_email` | string | Canonical form (lowercased; for Gmail: dots stripped, `+tag` removed) |
| `account` | string | Local part (before `@`) |
| `domain` | string | Domain part (after `@`) |
| `domain_age_days` | integer | How old the domain is |
| `smtp_provider` | string | Detected mail provider (`google`, `microsoft`, `yahoo`, `apple`, …) |
| `mx_found` | boolean | Domain has MX records |
| `mx_record` | string | Primary MX host |
| `mx_records` | string[] | MX hosts as an array (compatibility) |
| `firstname`, `lastname`, `gender`, `country`, `region`, `city`, `zipcode` | string | Append data when available |
| `processed_at`, `verified_at` | string | ISO-style timestamp of the check |

### Getting the output

```
# JSON
GET https://api.apify.com/v2/acts/<actor-id>/runs/<run-id>/dataset/items?format=json

# CSV — ready for Excel / Google Sheets / CRM import
GET https://api.apify.com/v2/acts/<actor-id>/runs/<run-id>/dataset/items?format=csv
```

---

## How it compares

| Feature | This Actor | Typical free validators |
| --- | --- | --- |
| Status + sub-status | ✅ | ✅ |
| Deliverability / quality score (0–100) | ✅ | partial |
| Risk level (low / medium / high) | ✅ | ⛔ |
| Disposable / role / catch-all flags | ✅ | ✅ |
| Gibberish detection (random strings) | ✅ | ⛔ |
| `+tag` / Gmail alias detection | ✅ | ⛔ |
| Normalized email (canonical form) | ✅ | ⛔ |
| Typo detection with suggested fix | ✅ | partial |
| MX host + provider | ✅ | ✅ |
| Domain age | ✅ | ⛔ |
| Append data (first/last name, geo) | ✅ | ⛔ |
| No API keys from the user | ✅ | ⛔ |

---

## Pricing & limits

- **Up to 50 emails per run** — enforced in the input schema.
- Default memory: **256 MB** per run.
- Typical run: 50 emails in ~60–90 seconds at default concurrency.
- Failed / unverifiable addresses are **skipped** — they are not written to the dataset, so you are never billed for checks that didn't return a verdict.

---

## Best practices

1. **Keep concurrency at 3–5** for the cleanest results. Higher concurrency can trigger upstream rate limits.
2. **Batch large lists** — call the actor multiple times with 50 addresses each rather than fighting the per-run cap.
3. **Treat `catch-all` as "probably deliverable but unverified"** — don't drop those leads; flag and re-check later.
4. **Always drop `spamtrap`, `abuse`, and `is_disposable=true`** — sending to these hurts your sender reputation.
5. **Use `is_possible_typo` + `did_you_mean`** to auto-suggest corrections at the form level, before verification is even needed.

---

## FAQ

**Q: Can I run this on 10 000 addresses?**
Split into 200 runs of 50 each, or chain runs via Apify's scheduler / integrations. The per-run cap keeps a single run responsive and avoids partial-failure confusion.

**Q: What happens if an address is ambiguous (catch-all / unknown)?**
You still get a record with `status=catch-all` or `status=unknown`, a `risk=medium|high`, and a `reason` field explaining why. No silent drops.

**Q: Will it detect invalid Cyrillic / Unicode addresses?**
Syntax check is RFC-style ASCII; Unicode local parts aren't flagged automatically. For IDN domains the Punycode form works.

**Q: Do you store or share the emails I validate?**
Emails live only in the run's own Apify dataset, under your account. After the run's retention window, they're deleted. Nothing is sent to third parties beyond what's strictly necessary to perform the verification.

---

## Contact

Questions, feature requests, bulk volume arrangements, custom fields: **[afrcanec@gmail.com](mailto:afrcanec@gmail.com)**

---

**Tags:** email verifier, email validator, email validation, email verification, bulk email checker, bulk email validator, email deliverability, email list cleaner, email list cleaning, email quality score, email risk score, disposable email detector, disposable email checker, catch all detection, catch all email, role account detector, typo correction, mx record lookup, smtp check, email hygiene, lead validation, cold email, bounce rate, sender reputation, mailbox verification, email syntax check, gibberish email detector