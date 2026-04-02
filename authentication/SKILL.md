---
name: authentication
description: >
  Deep reference for email authentication: SPF, DKIM, DMARC, and BIMI. Always use this
  skill before advising on anything involving DNS records for email, even if the question
  seems simple: authentication failures are the most common hidden cause of deliverability
  problems. Use it when: setting up a new sending domain, debugging why emails go to spam,
  reviewing DNS records, diagnosing DMARC alignment issues, interpreting authentication
  headers, reading DMARC aggregate reports, or advising on key rotation. If someone asks
  "why is my email going to spam" and authentication hasn't been verified yet, load this
  skill first.
---

# Email Authentication

Authentication proves to inbox providers that you are who you claim to be. It is the
foundation of deliverability. Without it, no amount of content or list quality will
reliably get you to the inbox.

> Related skills: `deliverability/SKILL.md` (overview), `monitoring/SKILL.md` (how to
> verify authentication is working in practice via Postmaster Tools)

The four layers, in order of implementation:

```
SPF   → Authorizes which servers can send on your behalf
DKIM  → Cryptographically signs your messages
DMARC → Tells inbox providers what to do when SPF/DKIM fail
BIMI  → Displays your brand logo in supporting inboxes
```

---

## SPF (Sender Policy Framework)

### What It Does

SPF lists the mail servers and services authorized to send email from your domain.
Inbox providers check the sending server's IP against this list on every inbound message.

### DNS Record Format

Published as a `TXT` record on your sending domain:

```
v=spf1 include:sendingprovider.com ip4:203.0.113.10 ~all
```

| Token                | Meaning                                                 |
| -------------------- | ------------------------------------------------------- |
| `v=spf1`             | SPF version (always this)                               |
| `include:domain.com` | Authorize all IPs listed in another domain's SPF record |
| `ip4:x.x.x.x`        | Authorize a specific IPv4 address                       |
| `ip6:x:x:x::x`       | Authorize a specific IPv6 address                       |
| `-all`               | Hard fail: reject anything not on the list              |
| `~all`               | Soft fail: mark as suspicious but accept                |
| `?all`               | Neutral: no opinion (avoid this)                        |

### The 10 DNS Lookup Limit

SPF resolves `include:` and `a:` tokens by making additional DNS lookups. The RFC
limits this to 10 total. Exceeding it causes a `permerror`, which often results in
SPF failure even if your IPs are correct.

Common ways to hit the limit: chaining multiple ESPs, using both marketing and
transactional providers, plus IT infrastructure like G Suite or Microsoft 365.

How to fix it:

- Audit your current lookups with tools like MXToolbox SPF checker.
- Consolidate where possible.
- Use SPF flattening services that resolve all includes to static IPs (tradeoff:
  requires maintenance when your provider's IPs change).

### Record Placement

- Publish on the subdomain you send from: `mail.yourdomain.com`, not just `yourdomain.com`.
- If you send from multiple subdomains, each needs its own SPF record.
- Your root domain also needs an SPF record (even if you do not send from it) to prevent
  spoofing: `v=spf1 -all`

### Starting Config vs. Production Config

- While warming up a new domain: use `~all` (soft fail). This lets you observe failures
  without hard-blocking legitimate mail.
- Once SPF and DKIM are confirmed working and DMARC reports are clean: switch to `-all`.

### Validation

```bash
dig TXT mail.yourdomain.com
# or
nslookup -type=TXT mail.yourdomain.com
```

---

## DKIM (DomainKeys Identified Mail)

### What It Does

DKIM adds a cryptographic signature to every outgoing email. The signature is verified
against a public key published in DNS. This proves the message was not tampered with
in transit and that the signing domain authorized it.

### How It Works

1. Your sending server signs the email headers and body with a private key.
2. The signature is added as a `DKIM-Signature` header.
3. The receiving server fetches your public key from DNS and verifies the signature.

### Key Strength

- Use **2048-bit keys** minimum. 1024-bit keys are deprecated and increasingly rejected
  by inbox providers.
- If your ESP only offers 1024-bit, push back or find an alternative.

### DNS Record Format

Published as a `TXT` record at `selector._domainkey.yourdomain.com`:

```
v=DKIM1; k=rsa; p=MIGfMA0GCSqGSIb3DQEBAQUAA4GN...
```

| Field     | Meaning                   |
| --------- | ------------------------- |
| `v=DKIM1` | Version (always this)     |
| `k=rsa`   | Key type                  |
| `p=...`   | Base64-encoded public key |

The **selector** is a label chosen by your ESP (e.g., `google`, `s1`, `mail`). It
allows multiple DKIM keys to coexist on the same domain.

### The `d=` Tag and Alignment

The `d=` tag in the `DKIM-Signature` header identifies the signing domain.
For DMARC alignment, this must match (or be a parent of) your From domain.

- From: `hello@mail.yourdomain.com`
- DKIM d=: `mail.yourdomain.com` or `yourdomain.com` (both align)
- DKIM d=: `sendingprovider.com` (does NOT align: configure a custom signing domain)

Always configure a custom signing domain through your ESP so DKIM signs as your domain,
not the ESP's generic domain.

### Key Rotation

- Rotate DKIM keys at least once per year.
- Rotation process: generate new key pair, publish new public key in DNS, switch
  signing to new private key, wait 48 hours for DNS propagation, then remove old public key.
- Keep old keys live for at least 72 hours after rotation to allow in-flight messages
  to verify.

### Validation

```bash
dig TXT selector._domainkey.yourdomain.com
```

Check that the record resolves and that the `p=` value matches what your ESP shows.

---

## DMARC (Domain-based Message Authentication, Reporting and Conformance)

### What It Does

DMARC ties SPF and DKIM together. It tells inbox providers what to do when a message
fails authentication, and gives you reporting so you can see who is sending email
purportedly from your domain.

### DNS Record Format

Published as a `TXT` record at `_dmarc.yourdomain.com`:

```
v=DMARC1; p=none; rua=mailto:dmarc@yourdomain.com; ruf=mailto:dmarc@yourdomain.com; pct=100;
```

| Tag        | Meaning                                                                                |
| ---------- | -------------------------------------------------------------------------------------- |
| `v=DMARC1` | Version (always this)                                                                  |
| `p=`       | Policy: `none`, `quarantine`, or `reject`                                              |
| `rua=`     | Aggregate report destination (daily digest of authentication results)                  |
| `ruf=`     | Forensic report destination (individual failure reports; not all providers send these) |
| `pct=`     | Percentage of mail to apply policy to (use `100` in production)                        |
| `sp=`      | Subdomain policy (defaults to same as `p=` if omitted)                                 |
| `adkim=`   | DKIM alignment mode: `r` (relaxed, default) or `s` (strict)                            |
| `aspf=`    | SPF alignment mode: `r` (relaxed, default) or `s` (strict)                             |

### DMARC Policies

- `p=none`: Monitor only. Messages that fail are still delivered. Essential starting point.
- `p=quarantine`: Failed messages go to the spam/junk folder.
- `p=reject`: Failed messages are rejected outright and not delivered.

### Rollout Sequence

Do not rush to `p=reject`. Done wrong, it will block legitimate mail.

```
Week 1-4:   p=none                  → Collect reports, identify all sending sources
Week 4-8:   p=none                  → Fix authentication gaps
Week 8-12:  p=quarantine; pct=10    → Apply to 10% of failing mail, monitor reports
Week 12-16: p=quarantine; pct=100   → Full quarantine enforcement
Week 16+:   p=reject                → Full rejection enforcement
```

### DMARC Alignment

DMARC requires either SPF or DKIM to "align" with the From domain.

- **SPF alignment**: The domain in the `Return-Path` (envelope sender) must match the From domain.
- **DKIM alignment**: The `d=` domain in the DKIM signature must match the From domain.
- **Relaxed alignment** (default): Subdomains are acceptable. `mail.yourdomain.com` aligns with `yourdomain.com`.
- **Strict alignment**: Exact domain match required.

If neither aligns, DMARC fails regardless of whether SPF and DKIM individually pass.

### Reading Aggregate Reports

Aggregate reports (RUA) are XML files sent daily by inbox providers. They show:

- How much mail was received claiming to be from your domain.
- SPF and DKIM pass/fail rates.
- Which IPs sent that mail.
- What DMARC disposition was applied.

Use a DMARC report parser (DMARC Analyzer, Valimail, dmarcian) rather than reading
raw XML. Key things to look for:

- Unexpected sending sources (a forgotten third-party service or active spoofing).
- High failure rates from known good sources (authentication misconfiguration).
- `disposition: reject` or `disposition: quarantine` on mail you intended to deliver.

### Subdomain Policy

If you have subdomains that do not send email, explicitly protect them:

```
_dmarc.mail.yourdomain.com → v=DMARC1; p=reject;
```

Or set `sp=reject` on your root DMARC record to apply a strict policy to all subdomains.

---

## BIMI (Brand Indicators for Message Identification)

### What It Does

BIMI displays your brand logo beside the sender name in supporting inbox clients
(Gmail, Apple Mail, Yahoo Mail, Fastmail). It is a trust signal to recipients, not
a deliverability factor in itself, but it rewards senders who have achieved strong
authentication.

### Requirements

1. DMARC must be at `p=quarantine` or `p=reject` with `pct=100`.
2. A valid SVG logo in BIMI-compliant format (SVG Tiny PS 1.2 spec).
3. For Gmail specifically: a VMC (Verified Mark Certificate) issued by an authorized
   certificate authority (DigiCert, Entrust).

### SVG Logo Requirements

- Format: SVG Tiny PS 1.2
- Shape: Square aspect ratio with a solid background (no transparent backgrounds).
- No embedded raster images, scripts, or animations.
- File size: under 32KB recommended.
- Hosted at a publicly accessible HTTPS URL.

### DNS Record Format

Published as a `TXT` record at `default._bimi.yourdomain.com`:

```
v=BIMI1; l=https://yourdomain.com/logo.svg; a=https://yourdomain.com/vmc.pem;
```

| Tag       | Meaning                                          |
| --------- | ------------------------------------------------ |
| `v=BIMI1` | Version                                          |
| `l=`      | URL of your SVG logo                             |
| `a=`      | URL of your VMC certificate (required for Gmail) |

Without a VMC, BIMI will work in some clients (Yahoo, Fastmail) but not Gmail.

### Is BIMI Worth It?

- If you already have `p=reject` DMARC and a registered trademark: yes, pursue it.
- If you are still in authentication rollout: focus on SPF/DKIM/DMARC first. BIMI
  has no deliverability benefit until those are solid.

---

## Common Authentication Failures and Fixes

| Failure                           | Likely Cause                                  | Fix                                                |
| --------------------------------- | --------------------------------------------- | -------------------------------------------------- |
| SPF `permerror`                   | Exceeded 10 DNS lookup limit                  | Flatten SPF or consolidate providers               |
| SPF `fail` on known good IP       | IP not listed in SPF record                   | Add `ip4:` or `include:` for the missing server    |
| DKIM signature invalid            | Key mismatch or header modified in transit    | Check that no intermediary is modifying headers    |
| DKIM `d=` misaligned              | Signing as ESP domain instead of your domain  | Configure custom signing domain with your ESP      |
| DMARC fail despite SPF/DKIM pass  | Alignment failure                             | Ensure `d=` or envelope sender matches From domain |
| DMARC reports show unknown source | Forgotten ESP, forwarding, or active spoofing | Investigate source IP, add to SPF or block         |

---

## Quick Validation Commands

```bash
# Check SPF
dig TXT mail.yourdomain.com

# Check DKIM (replace 'selector' with your actual selector)
dig TXT selector._domainkey.yourdomain.com

# Check DMARC
dig TXT _dmarc.yourdomain.com

# Check BIMI
dig TXT default._bimi.yourdomain.com
```

Or use MXToolbox (mxtoolbox.com) for a browser-based check with human-readable output.
