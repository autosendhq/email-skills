---
name: email-skills
description: >
  Master reference for sending emails that reach the inbox. Always load this skill first
  for any email-related task, it will orient you quickly and route you to the right
  sub-skill. Use it for: email sending setup, inbox placement, spam avoidance, domain
  reputation, email infrastructure, writing or reviewing email copy, diagnosing why
  emails go to spam, building email workflows, or advising on list management. If the
  task touches email in any way, start here before doing anything else.
---

# Email Skills

A modular skill set for AI agents writing, reviewing, or advising on email. The goal
is inbox placement, not just sending. Every decision here connects back to that.

This is the entry point. Read this first, then load the relevant sub-skill for deeper
guidance on a specific topic.

---

## The Deliverability Stack

Inbox placement depends on four pillars working together. A failure in any one of them
can undo the other three.

```
1. Authentication   → Proves you are who you say you are
2. Sender Reputation → Proves you are a responsible sender
3. Content Quality  → Proves your email is wanted
4. List Quality     → Proves your recipients consented and are real
```

---

## Sub-Skills (Load as Needed)

| Topic                           | Sub-Skill                      | When to Use                                         |
| ------------------------------- | ------------------------------ | --------------------------------------------------- |
| SPF, DKIM, DMARC, BIMI          | `authentication/SKILL.md`      | Setting up or debugging email authentication        |
| Subject lines, HTML, copy       | `content/SKILL.md`             | Writing or reviewing email content                  |
| CAN-SPAM, GDPR, CASL            | `compliance/SKILL.md`          | Any question touching legal requirements or consent |
| Validation, suppression, sunset | `list-hygiene/SKILL.md`        | Managing contacts, bounces, or unsubscribes         |
| OTP, receipts, password reset   | `transactional/SKILL.md`       | Building or writing transactional email flows       |
| Drip, campaigns, segmentation   | `marketing-campaigns/SKILL.md` | Writing or planning marketing email                 |
| Drip sequences, lifecycle flows | `email-automation/SKILL.md`    | Choosing or building automated email sequences      |
| Postmaster Tools, blacklists    | `monitoring/SKILL.md`          | Diagnosing issues or setting up monitoring          |

---

## Core Principles (Always Apply)

These apply regardless of which sub-skill you load.

**Separate transactional from marketing.** Different sending streams, different subdomains.
A spam complaint on your marketing stream should never affect transactional delivery.

**Send from a subdomain, not your root domain.** Use `mail.yourdomain.com` or
`em.yourdomain.com`. This isolates email reputation from your web domain.

**Authenticate before you send anything.** SPF, DKIM, and DMARC must be in place before
volume matters. See `authentication/SKILL.md`.

**List quality beats list size.** Sending to disengaged or invalid addresses actively
damages your domain reputation. See `list-hygiene/SKILL.md`.

**Open rate is not a reliable metric.** Apple Mail Privacy Protection inflates open rates
by pre-fetching tracking pixels. Use click rate, reply rate, and conversion rate instead.

**Consistency matters more than volume.** Sudden spikes in sending volume look like
compromised accounts to inbox providers. Ramp gradually and send at a predictable cadence.

**Google's spam rate threshold is 0.08%.** Above this, Gmail delivery degrades. Above 0.1%,
mail starts getting blocked. Monitor this daily via Google Postmaster Tools.

---

## Transactional vs. Marketing: The Key Distinction

| Dimension            | Transactional                            | Marketing             |
| -------------------- | ---------------------------------------- | --------------------- |
| Trigger              | User action                              | Sender-initiated      |
| Unsubscribe required | No (best practice: include support path) | Yes, legally required |
| Consent required     | Implied by account creation              | Express opt-in        |
| Promotional content  | Incidental only                          | Yes                   |
| Sending stream       | Separate                                 | Separate              |

Common transactional types: password reset, 2FA/OTP, order confirmation, shipping update,
billing receipt, account alert, email verification.

---

## Pre-Send Checklist (Quick Reference)

**Authentication**

- [ ] SPF record published and valid
- [ ] DKIM signing active and selector resolving
- [ ] DMARC policy published (`p=none` minimum to start)

**Content**

- [ ] Subject line: under 60 chars, no spam trigger words, not misleading
- [ ] Preview text set explicitly (not left to the client to guess)
- [ ] Plain-text version included alongside HTML
- [ ] No JavaScript, no URL shorteners
- [ ] All images have alt text
- [ ] Physical mailing address in footer (required for commercial email)
- [ ] Unsubscribe link present and functional (marketing only)

**List**

- [ ] Addresses validated before import
- [ ] Suppression list applied before send
- [ ] Consent documented for all marketing contacts
