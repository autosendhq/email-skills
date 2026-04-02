---
name: list-hygiene
description: >
  Best practices for maintaining healthy email contact lists. Load this skill whenever
  working with contact data, diagnosing bounce or complaint rate problems, or importing
  lists, poor list quality is a leading cause of deliverability failure and it is easy
  to damage a domain's reputation with a single bad send to a stale list. Use it for:
  email address validation, bounce classification and handling, building suppression lists,
  processing unsubscribes, sunsetting inactive contacts, re-engagement campaign design,
  list import procedures, spam trap avoidance, or diagnosing why bounce or complaint rates
  are high. Also load it when asked whether to delete or suppress a contact.
---

# Email List Hygiene

List quality is a direct input to deliverability. Inbox providers observe your sending
behavior, bounce rate, complaint rate, engagement, and use it to score your domain
reputation. A clean list with genuine subscribers outperforms a large, stale list every
time.

> Related skills: `compliance/SKILL.md` (consent requirements, GDPR right to erasure),
> `monitoring/SKILL.md` (how to track bounce and complaint rates over time)

---

## Email Validation

Validate email addresses before they enter your system, not after the first failed send.

### Why Validation Matters

- Hard bounces damage your sender reputation immediately
- Invalid addresses can include spam traps, addresses maintained specifically to catch
  senders with poor list hygiene (more on this below)
- Role addresses (info@, support@, admin@) are rarely monitored by individuals and
  frequently feed complaint systems

### What to Validate

**Format validation** (client-side, immediate):

- Correct email format: `local@domain.tld`
- No spaces, no double `@`, valid TLD
- This is the minimum, it catches typos but not invalid addresses

**DNS/MX record check** (server-side, at submission):

- Does the domain exist?
- Does it have valid MX records (can it receive email)?
- A domain with no MX record cannot receive email; reject immediately

**Mailbox validation** (real-time API):

- Does the specific mailbox exist at that domain?
- Uses SMTP-level verification without sending an email
- Not 100% accurate, some servers block SMTP verification, but significantly reduces
  invalid addresses
- Some validation services also flag disposable email domains (mailinator.com,
  guerrillamail.com, etc.) and role addresses

**Catch-all detection:**

- Some domains accept all email regardless of whether the mailbox exists
  (`*@domain.com` catches everything)
- Validation services flag these as "catch-all" or "accept-all"
- Treat catch-all addresses with caution, they pass format and DNS checks but may
  not belong to a real person

### Role Address Filtering

Block or flag these patterns at signup:

- `info@`, `contact@`, `hello@`, `support@`, `help@`
- `admin@`, `administrator@`, `webmaster@`, `postmaster@`
- `noreply@`, `no-reply@`, `donotreply@`
- `sales@`, `billing@`, `accounts@`, `team@`

Role addresses are shared inboxes. They do not belong to an individual, have no personal
engagement, and often pipe directly into ticketing or spam-report systems.

### Disposable Email Domains

Some users create accounts with throwaway addresses to avoid giving their real email.
Disposable addresses:

- Will not engage with your email
- Will be abandoned within days
- May bounce after the address expires

Maintain a blocklist of known disposable email providers and reject them at signup.
Many email validation services include this check.

---

## Bounce Management

### Hard Bounces

A hard bounce indicates permanent delivery failure:

- The email address does not exist
- The domain does not exist
- The receiving server has permanently rejected the address

**Action: Suppress immediately. Never retry.**

One hard bounce is enough to suppress. Continuing to send to a hard-bounced address
signals to inbox providers that you have no list hygiene process, and some hard-bounced
addresses are spam traps.

### Soft Bounces

A soft bounce indicates a temporary failure:

- Mailbox is full (over quota)
- Receiving server is temporarily unavailable
- Message too large

**Action: Retry with exponential backoff.**

- Retry after 1 hour, then 24 hours, then 72 hours
- After 3-5 failures over 72+ hours, convert to hard bounce and suppress
- Track soft bounce frequency per address. A contact that soft-bounces repeatedly
  over weeks is likely an abandoned mailbox.

### Bounce Rate Thresholds

| Rate     | Status   | Action                                                  |
| -------- | -------- | ------------------------------------------------------- |
| Below 1% | Healthy  | Continue normal sending                                 |
| 1-2%     | Caution  | Review recent list additions; validate before next send |
| 2-5%     | Problem  | Pause sending; clean list before resuming               |
| Above 5% | Critical | Stop sending immediately; full list audit required      |

Bounce rates above 2% are a strong signal to inbox providers that your list has poor
hygiene. Gmail and Outlook will begin throttling or blocking delivery.

### Bounce Categories (Technical Reference)

SMTP bounce codes useful for classification:

| Code                    | Category                       | Action                            |
| ----------------------- | ------------------------------ | --------------------------------- |
| 550, 551, 553           | User unknown / does not exist  | Hard bounce, suppress             |
| 552, 554                | Message rejected (spam/policy) | Investigate content; may suppress |
| 421, 450, 451           | Temporary server issue         | Soft bounce, retry                |
| 452                     | Mailbox full                   | Soft bounce, retry                |
| 421 + blacklist message | IP/domain blacklisted          | Stop sending; check blacklists    |

---

## Suppression Lists

### What a Suppression List Is

A suppression list is a permanent record of addresses you must never send to. It is not
a list to re-enable later, it is a do-not-contact list that takes precedence over all
other lists and segments.

### What Belongs on the Suppression List

- Hard bounced addresses (added immediately and permanently)
- Spam complaints (added immediately and permanently)
- Manual unsubscribe requests (added immediately)
- Addresses identified as spam traps
- Addresses added by admin decision (e.g., a known problematic domain)

### Suppression List Integrity

**Never bypass suppression.** This is the most critical list hygiene rule:

- Do not re-import a suppressed address in a new contact list
- Do not import a list from another product or brand that does not share your suppression state
- Do not use a different sending domain to "restart" with suppressed contacts
- Do not segment around suppressed contacts to reach them via a different campaign type

Bypassing suppression is:

1. A compliance violation (CAN-SPAM, GDPR, CASL all require honoring opt-outs)
2. A deliverability catastrophe, spam complaints from people who already unsubscribed
   are among the strongest negative signals a sender can generate
3. A potential account termination offense with your ESP

**On list imports:** Every time you import contacts from an external source (another tool,
a CRM, an events platform), cross-reference the import against your full suppression list
before the first send. Do not rely on the exporting system to have maintained suppression.

### Suppression Propagation

Suppression must propagate across all sending systems, brands, and products that share
the same sending domain. A contact who unsubscribed from your newsletter must not receive
email from a second brand you run that uses the same domain.

If you run multiple products on separate domains, document whether suppression is shared
or separate, and communicate this clearly in your consent language.

---

## Spam Traps

Spam traps are email addresses used by inbox providers and anti-spam organizations to
identify senders with poor list hygiene. Sending to a spam trap is a serious reputation
signal.

### Types

**Pristine traps (honeypots):**

- Addresses that were never owned by a real person
- Published on websites (invisible to humans, visible to scrapers)
- Hitting one means your list was built by scraping, an immediate red flag

**Recycled traps:**

- Addresses that belonged to real users, were abandoned, bounced for a period,
  and were then converted into traps by inbox providers
- A valid email address 3 years ago may be a spam trap today
- Sending to recycled traps means your list is stale

**Typo traps:**

- Intentionally misspelled domains: `gnail.com`, `yahooo.com`, `hotmial.com`
- Indicates you are not validating email format at signup

### How to Avoid Spam Traps

- Validate email addresses at signup (reduces pristine and typo traps)
- Sunset inactive contacts before they become recycled traps (the key protection)
- Never scrape or purchase lists
- Use double opt-in for marketing lists (pristine traps will never confirm)

### What to Do If You Hit a Spam Trap

- You may not know immediately. Indicators include sudden drops in domain reputation on
  Google Postmaster Tools, or a Spamhaus listing.
- Stop sending to the affected list segment immediately.
- Identify the source: when were these contacts added, and how?
- Remove contacts that have never engaged and are from suspicious sources.
- Do not mass-delete, suppress, so you have a record.

---

## Sunsetting Inactive Contacts

An inactive contact is someone who has not opened, clicked, or otherwise engaged with
your email in a defined period. They are:

- Dragging down your engagement rate (which inbox providers observe)
- Potentially becoming or already spam traps
- Using your sending quota for zero return

### Defining Inactivity

Set your sunset threshold based on your sending frequency:

- **Weekly senders:** 3-6 months with no engagement
- **Monthly senders:** 6-12 months with no engagement
- **Occasional senders:** 12 months with no engagement

Note: with Apple MPP inflating open rates, opens are an unreliable signal. Use clicks
as the primary engagement signal for defining inactivity.

### Re-Engagement Campaign

Before sunsetting a contact, run a re-engagement sequence:

**Email 1 (day 0):**
Subject: "Still want to hear from us?"
Content: Acknowledge the silence. Remind them of the value. Clear yes/no CTA.

- "Yes, keep me subscribed" (re-sets their engagement, keeps them on list)
- "No, unsubscribe me" (unsubscribe link)
  If no action, continue to email 2.

**Email 2 (day 7):**
Subject: "Last chance, we're about to say goodbye"
Content: Final notice. You will be removing them from the list if no action.
If no action after this email, suppress the contact.

**Do not guilt-trip.** Tone should be matter-of-fact and respectful. A clean unsubscribe
is better than a resentful subscriber who eventually marks you as spam.

### After Sunsetting

- **Suppress, do not delete.** Deleting removes the record that you ever had this contact,
  which creates a data integrity problem and makes it impossible to honor future re-engagement
  or re-import correctly.
- The suppressed record should note: reason = "sunset", date = [date]
- If a sunsetted contact re-subscribes through a new form fill, they can be re-added
  (their fresh consent supersedes the sunset).

---

## List Import Procedures

Importing contacts from another system carries risk. Follow this process:

### Before Importing

1. **Verify consent.** Can you document when and how each contact consented to receive
   email from you specifically? "We bought this list" and "they signed up for our partner's
   newsletter" are not valid.

2. **Check the age of the list.** Contacts last emailed more than 12 months ago should
   be treated with extra scrutiny. The older the list, the higher the bounce risk.

3. **Validate the list.** Run the full list through an email validation service before
   importing. Remove invalids, role addresses, and disposables.

4. **Cross-reference suppression.** Every address on your suppression list must be excluded
   from the import.

### First Send to an Imported List

- **Do not blast the full list immediately.** Send to a small segment (5-10%) first and
  monitor bounce rate and complaint rate for 24-48 hours before proceeding.
- If metrics are clean, proceed in batches.
- If bounce rate exceeds 2% or complaint rate exceeds 0.05% in the test segment, stop
  and re-evaluate the list quality before continuing.

### Lists You Should Never Import

- Purchased lists
- Scraped lists (from websites, LinkedIn, directories, etc.)
- Lists shared by another company or brand without explicit, documented consent transfer
- Lists where the consent was for a different product, brand, or purpose

---

## Contact List Health Dashboard

Track these metrics regularly to maintain visibility into list health:

| Metric                               | Healthy  | Caution    | Action Required                          |
| ------------------------------------ | -------- | ---------- | ---------------------------------------- |
| Hard bounce rate                     | < 1%     | 1-2%       | > 2%                                     |
| Soft bounce rate                     | < 3%     | 3-5%       | > 5%                                     |
| Spam complaint rate                  | < 0.05%  | 0.05-0.08% | > 0.08%                                  |
| Unsubscribe rate                     | < 0.5%   | 0.5-1%     | > 1%                                     |
| List growth rate (net)               | Positive | Flat       | Negative (shrinking faster than growing) |
| Active contacts (clicked in 90 days) | > 20%    | 10-20%     | < 10%                                    |

A healthy list is not the largest list, it is the most engaged list.
