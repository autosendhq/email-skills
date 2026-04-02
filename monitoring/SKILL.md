---
name: monitoring
description: >
  Reference for monitoring email deliverability and diagnosing inbox placement failures.
  Always load this skill when deliverability has dropped unexpectedly or when setting up
  a new sending domain, problems caught early are far easier to fix than reputation
  damage that has built up over weeks. Use it for: setting up Google Postmaster Tools or
  Microsoft SNDS, investigating why emails are going to spam, checking blacklist status,
  recovering from a spam complaint spike, reading postmaster data, interpreting bounce
  codes, or running a systematic diagnosis of a deliverability problem. Also load it when
  asked what monitoring to set up before a first send.
---

# Email Monitoring and Deliverability Diagnosis

You cannot fix what you cannot see. Monitoring is the feedback loop that tells you
whether your authentication, content, and list quality are actually working, and alerts
you when something breaks before it becomes a serious reputation problem.

> Related skills: `authentication/SKILL.md` (fixing SPF/DKIM/DMARC when monitoring
> reveals failures), `list-hygiene/SKILL.md` (fixing bounce and complaint rates),
> `marketing-campaigns/SKILL.md` (interpreting per-campaign metrics)

---

## Tools to Set Up on Day One

These are non-negotiable if you send more than a few hundred emails per month.

### Google Postmaster Tools

**URL:** postmaster.google.com

The most important monitoring tool available. Google provides visibility into how Gmail
specifically sees your sending domain. Since Gmail accounts for a large share of most
lists, this is your primary reputation signal.

**What it shows:**

- **Domain reputation**: High / Medium / Low / Bad. This is the headline metric.
- **IP reputation**: Reputation of the specific IPs sending your mail.
- **Spam rate**: The percentage of Gmail users who marked your email as spam in the last
  rolling window. The most actionable single number in email deliverability.
- **Authentication**: Whether your SPF, DKIM, and DMARC are passing.
- **Delivery errors**: Specific SMTP error codes Gmail is returning when rejecting or
  deferring your mail.
- **Encryption**: Whether your mail is being sent over TLS (should be 100%).
- **Feedback loop**: Complaint signals from Gmail users.

**Setup:**

1. Go to postmaster.google.com.
2. Add your sending domain (e.g., `mail.yourdomain.com`).
3. Verify ownership by adding a TXT record to your DNS.
4. Data begins populating once you send a meaningful volume of email to Gmail addresses.

**Limitation:** Data is only available when sending volume to Gmail is above a threshold
(typically a few hundred emails per day). Low-volume senders may see limited data.

### Microsoft SNDS (Smart Network Data Services)

**URL:** sendersupport.olc.protection.outlook.com/snds

Equivalent to Postmaster Tools for Outlook, Hotmail, and Live. Shows complaint rates
and trap hits from Microsoft's network.

**Setup:**

- Register with your sending IP address (not domain).
- If you send from a shared IP pool managed by your ESP, your ESP may provide access.
- If you send from dedicated IPs, register those IPs directly.

**What it shows:**

- Filter result: Green (OK), Yellow (investigate), Red (problem).
- Trap hits: Emails landing in Microsoft's spam trap network. Even a few trap hits are
  a serious warning sign.
- Complaint rate: Similar to Google's spam rate metric.

### Blacklist Monitoring

A blacklist listing means your IP or domain is on a list that inbox providers and spam
filters use to block or score incoming mail.

**Key blacklists to monitor:**

- **Spamhaus SBL/XBL/PBL**: The most impactful. A Spamhaus listing will block delivery
  to most major inbox providers.
- **Barracuda BRBL**: Used by many corporate email systems.
- **SORBS**: Moderately impactful.
- **SpamCop**: User-reported spam database.
- **Invaluement**: Used by some providers.

**How to monitor:**

- MXToolbox (mxtoolbox.com/blacklists.aspx): Checks against 100+ blacklists in one query.
- Use the domain lookup for domain-based blacklists and IP lookup for IP-based blacklists.
- Set up weekly automated checks. MXToolbox and similar tools offer monitoring with alerts.

### Feedback Loops (FBL)

Feedback loops are agreements with inbox providers where they send you a complaint report
every time one of their users marks your email as spam.

**Available FBLs:**

- **Yahoo/AOL**: Apply at senders.yahooinc.com. Most impactful to set up.
- **Comcast**: Available for dedicated IP senders.
- **Outlook/Hotmail**: Use JMRP (Junk Mail Reporting Program) at sendersupport.olc.protection.outlook.com.
- **Gmail**: Does not offer a traditional FBL. Use Postmaster Tools instead.

**What to do with FBL reports:**

- Immediately suppress the complaining address.
- If complaint volume is suddenly high from a specific campaign, pause further sends
  from that campaign and investigate the list or content.

---

## Key Metrics and Thresholds

| Metric                         | Healthy | Investigate | Critical  |
| ------------------------------ | ------- | ----------- | --------- |
| Spam complaint rate (Gmail)    | < 0.08% | 0.08-0.1%   | > 0.1%    |
| Hard bounce rate               | < 0.5%  | 0.5-2%      | > 2%      |
| Soft bounce rate               | < 2%    | 2-5%        | > 5%      |
| Unsubscribe rate               | < 0.2%  | 0.2-0.5%    | > 0.5%    |
| Domain reputation (Postmaster) | High    | Medium      | Low / Bad |

### Spam Rate vs. Spam Folder Placement

These are different things. Spam rate (in Postmaster Tools) is the percentage of Gmail
users who actively marked your email as spam. Spam folder placement is whether your email
is delivered to the inbox or spam folder at all. Both matter, but they have different causes:

- High spam rate → Your content or list is generating complaints. Fix: list hygiene,
  segmentation, unsubscribe flow, content relevance.
- Spam folder placement without high complaint rate → Authentication, domain reputation,
  or content spam scoring. Fix: check authentication records, review content, check blacklists.

---

## Reading Google Postmaster Tools

### Domain Reputation

- **High**: Good standing. Mail is being delivered to inbox.
- **Medium**: Some negative signals. Monitor closely. Mail may be going to spam for some users.
- **Low**: Significant problems. Expect substantial spam folder placement. Immediate action required.
- **Bad**: Severe reputation damage. Mail is being largely rejected or blocked.

Recovery from Low or Bad is possible but slow, typically 4-8 weeks of clean sending
with low complaint rates, good engagement, and no blacklist listings.

### Spam Rate Graph

- The graph shows your daily spam rate as a percentage.
- Target: consistently below 0.08%.
- A single spike above 0.1% can trigger Gmail throttling or blocking.
- If you see a spike, correlate it with what you sent that day. Which campaign or
  sequence was active?

### Delivery Errors

When Gmail rejects or defers your mail, Postmaster Tools shows the SMTP response codes:

| Code       | Meaning                                             |
| ---------- | --------------------------------------------------- |
| 421        | Temporary deferral, Gmail is throttling your mail   |
| 550        | Permanent rejection, specific message or IP blocked |
| 550 5.7.1  | Gmail has blocked sending to this address or domain |
| 421 4.7.0  | IP reputation issue                                 |
| 550 5.7.26 | DMARC authentication failure                        |
| 550 5.7.28 | IP not authorized to send (SPF failure)             |

A pattern of 421 errors at high volume means Gmail is rate-limiting your domain. Reduce
sending rate and focus on improving engagement quality before scaling back up.

---

## Diagnosing a Deliverability Problem

When inbox placement drops or you receive complaints, follow this sequence:

### Step 1: Check Authentication

```bash
dig TXT mail.yourdomain.com          # SPF
dig TXT selector._domainkey.yourdomain.com  # DKIM
dig TXT _dmarc.yourdomain.com        # DMARC
```

Or use MXToolbox. Verify that SPF, DKIM, and DMARC are all passing. A DNS change,
domain migration, or ESP configuration change can silently break authentication.

### Step 2: Check Blacklists

Run your sending domain and sending IP against MXToolbox blacklist checker.
A new blacklist listing, especially Spamhaus, will explain sudden delivery failures.

### Step 3: Check Postmaster Tools

- What is the current domain reputation?
- Is the spam rate elevated?
- Are there delivery errors?
- Correlate timing of any drops with your send history.

### Step 4: Review Recent Sends

- Did volume spike suddenly? Large unexpected volume increases look like account
  compromise to inbox providers.
- Did you send to a new or imported list? Imported lists often contain invalid
  addresses and spam traps.
- Did content change significantly? A new template or copy direction may have
  introduced spam trigger patterns.
- Did you send to a very old or unengaged segment?

### Step 5: Check Bounce and Complaint Rates

Pull reports from your ESP for recent campaigns:

- Hard bounce rate above 2% suggests list quality problems.
- Complaint rate above 0.1% is the direct cause of Gmail reputation degradation.

### Step 6: Use a Spam Testing Tool

Tools like Mail-Tester (mail-tester.com) and GlockApps let you send a test email and
receive a detailed spam score breakdown, including authentication results, content issues,
blacklist status, and inbox placement predictions.

GlockApps additionally runs seed list tests that show where your mail actually lands
(inbox, spam, or missing) across Gmail, Outlook, Yahoo, and other providers. This is
the most direct way to diagnose inbox placement before and after fixes.

---

## Blacklist Recovery

### Spamhaus

1. Go to check.spamhaus.org to identify the specific list (SBL, XBL, PBL).
2. Read the listing reason carefully.
3. Fix the root cause before requesting removal:
   - SBL: Usually spam-related complaints or spam trap hits. Fix list quality and sending practices.
   - XBL: IP is listed in a botnet or compromised host database. Check your sending server for malware.
   - PBL: IP is a dynamic/residential IP not intended for sending. Use your ESP's IP infrastructure.
4. Submit a removal request at spamhaus.org. Be specific about what caused the listing and what you have fixed.
5. Spamhaus removals typically process within 24-48 hours if the root cause is resolved.

### Barracuda

1. Check listing at barracudacentral.org/lookups.
2. If listed, submit removal request at the same site.
3. Barracuda typically processes requests within 12-24 hours.
4. If relisted quickly, it means the underlying behavior has not changed.

### SpamCop

SpamCop listings are time-based and typically expire within 24-48 hours if you stop
sending to the address that generated the complaint. Manual removal is not available.

### General Blacklist Recovery Principles

- Fix first, request removal second. Requesting removal without fixing the root cause
  will result in relisting, often within hours.
- Common root causes: compromised account, purchased list, sudden volume spike, high
  bounce rate, spam trap hits.
- After relisting following removal: the blacklist operator may decline future requests.
  Repeated listings lead to permanent blocks on some networks.

---

## Proactive Monitoring Routine

### Daily (automated alerts)

- Blacklist status for sending domain and IPs (set up MXToolbox alerts)
- Bounce rate on any campaign sent in the last 24 hours

### Weekly

- Review Google Postmaster Tools: domain reputation, spam rate trend, delivery errors
- Review Microsoft SNDS: filter result, trap hits
- Check hard bounce rate across all campaigns for the week
- Review FBL reports for complaint volume and patterns

### Monthly

- Full authentication audit: confirm SPF, DKIM, DMARC are passing correctly
- Review suppression list growth rate
- Identify and segment inactive contacts
- Review domain warm-up progress (if applicable)
- Run a spam test via Mail-Tester or GlockApps on a sample campaign

---

## Early Warning Signs

These are not emergencies yet, but act on them before they become one:

- **Bounce rate creeping above 1%**: List quality is degrading. Run validation and clean
  the list before the next large campaign.
- **Unsubscribe rate above 0.3%**: Content relevance or frequency is off for a segment.
  Review what you sent and to whom.
- **Postmaster Tools reputation drops from High to Medium**: Something changed in your
  sending behavior or reputation. Investigate before it drops further.
- **Engagement rate declining week over week**: Could be content fatigue, poor segmentation,
  or the beginning of a list quality problem.
- **A single campaign generates disproportionate unsubscribes or complaints**: That
  specific send had a problem, list, content, or timing. Do not repeat the pattern.
