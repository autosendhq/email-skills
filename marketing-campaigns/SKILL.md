---
name: marketing-campaigns
description: >
  Best practices for planning, writing, and sending marketing email campaigns. Load this
  skill for any marketing email task, a poorly planned campaign can damage domain
  reputation for months, while a well-executed one builds it. Use it for: drip sequences,
  broadcast campaigns, segmentation strategy, personalization, CTA design, send timing,
  frequency management, re-engagement campaigns, welcome sequences, onboarding flows,
  A/B testing, or unsubscribe UX. Also load it when someone asks how to improve campaign
  performance, reduce unsubscribe rates, or structure a sequence that converts without
  hurting deliverability.
---

# Email Marketing Campaigns

Marketing email is sender-initiated. That distinction shapes everything: consent
requirements, unsubscribe obligations, content rules, and the risk profile of every
send. Done well, marketing email drives meaningful revenue and retention. Done poorly,
it damages your domain reputation in ways that take months to recover.

> Related skills: `compliance/SKILL.md` (consent, CAN-SPAM, GDPR, unsubscribe law),
> `list-hygiene/SKILL.md` (segmentation data, suppression, sunset policy),
> `content/SKILL.md` (subject lines, HTML structure, spam trigger words),
> `monitoring/SKILL.md` (tracking complaint rate after sends)

---

## The One Email, One Goal Rule

Every marketing email should have a single primary call-to-action.

Multiple competing CTAs dilute click rate, confuse the reader, and make it harder to
measure what is working. Supporting links (view in browser, social links, footer nav)
are fine. But the email should have one clear job.

Good: "Start your free trial"
Bad: "Start your trial / Read our blog / Follow us on X / Upgrade your plan / Refer a friend"

If you have multiple things to communicate, that is a sign you need multiple emails
in a sequence, not one crowded email.

---

## Campaign Types

### Welcome Sequence

Sent immediately after a new subscriber opts in or a user creates an account.

- Send the first email within minutes of signup. Engagement is highest at this moment.
- Welcome emails have some of the highest open and click rates of any email type. Use
  them to establish expectations: what will you send, how often, and what value does
  the reader get?
- Introduce the most important feature, resource, or next step, not everything at once.
- Do not pitch aggressively in the first email. Build trust before asking for anything.

Typical welcome sequence structure:

```
Email 1 (immediate): Welcome + deliver the promised value (guide, trial, resource)
Email 2 (day 2-3):   Most important use case or success story
Email 3 (day 5-7):   Social proof + address the most common objection
Email 4 (day 10-14): Soft CTA, trial upgrade, booking a demo, or next meaningful action
```

### Onboarding Drip

Behavior-triggered sequence designed to guide a new user to activation.

- Triggered by account creation or product event, not a calendar schedule.
- Each email should be triggered by what the user has or has not done in the product.
- Stop the sequence when the user completes the target action. Sending "Get started"
  emails to someone who is already active is a fast way to generate unsubscribes.
- Keep emails focused on a single activation milestone.

### Broadcast Campaign

One email sent to a segment or full list at one time.

- Higher risk profile than drips because you are sending to many people at once.
- Segment before sending. Sending a campaign to your full list regardless of relevance
  is the most common cause of elevated spam complaint rates.
- Stagger large sends in batches (e.g., 25% every 2 hours) rather than sending to
  everyone simultaneously. This smooths reputation signals and lets you catch problems early.
- Monitor complaint rate and click rate in the first hour. Pause and investigate if
  complaint rate exceeds 0.05%.

### Re-Engagement Campaign

Sent to contacts who have not engaged with any email in 6-12 months.

- Run this before sunsetting inactive contacts, not as a permanent strategy.
- Be direct: "We noticed you haven't opened our emails in a while. Do you still want
  to hear from us?" Honest language performs better than manufactured urgency.
- Offer an easy way to stay subscribed and an easy way to unsubscribe.
- Two to three emails maximum. If there is no engagement after the sequence, suppress
  the contact.
- Do not try to re-engage with a promotional offer alone. Unengaged contacts are more
  likely to mark you as spam than to convert.

### Promotional / Announcement Campaign

Used for product launches, feature announcements, limited offers, events.

- Lead with the news, not the sell. What changed? Why does it matter to this reader?
- Segment by relevance. A feature announcement is most relevant to users who would
  actually use that feature.
- For time-sensitive offers: state the deadline clearly and only once. Countdown timers
  in email are supported in some clients but degrade in others, always include the
  plain date as a fallback.

---

## Segmentation

Sending to your entire list every time is a deliverability and engagement mistake.
Segmentation lets you send more relevant email to smaller groups, which improves
engagement rates and reduces complaints.

### Effective Segmentation Dimensions

**By engagement level:**

- Active (opened or clicked in last 90 days)
- Lapsed (no engagement in 90-180 days)
- Inactive (no engagement in 180+ days)

Send campaigns primarily to active contacts. Lapsed contacts get re-engagement sequences.
Inactive contacts are queued for sunset.

**By behavior:**

- Has/has not completed a key product action (activated, upgraded, invited a teammate)
- Has/has not purchased
- Viewed a specific page or feature

**By contact attributes:**

- Plan type (free vs. paid)
- Industry or company size (if collected)
- Geography (for time-zone-adjusted sending or region-specific content)
- Signup source (different expectations from different acquisition channels)

**By lifecycle stage:**

- New (signed up in last 30 days)
- Active customer
- At-risk (signs of churn: declining usage, support tickets)
- Churned

### Minimum Viable Segmentation

If you can only do one thing: separate engaged from unengaged contacts and stop
sending broadcast campaigns to unengaged contacts. This single change will improve
deliverability more than almost anything else.

---

## Personalization

### What Works

- First name in subject line or opening line: modest lift, easy to implement.
- Content adapted to plan type, behavior, or stage: significant lift, worth the effort.
- Dynamic content blocks (show different content to different segments in one send):
  powerful when used for genuinely different use cases, not just cosmetic differences.

### What to Avoid

- Personalization that exposes that you are watching: "We noticed you visited our pricing
  page three times" reads as surveillance.
- Personalization fallbacks that break when data is missing: "Hi [FIRST_NAME]" in the
  inbox is worse than no personalization. Always set a fallback: "Hi there" or "Hi,"

### Personalization Token Syntax

Token syntax varies by ESP. Common formats:

```
{{first_name}}           → Handlebars (common in many ESPs)
{first_name}             → Single brace (some platforms)
*|FNAME|*                → Mailchimp legacy
{{ contact.first_name }} → HubSpot / some newer platforms
```

Always test with a real send to yourself before deploying. Broken tokens in subject
lines are one of the most visible email mistakes.

---

## Send Timing and Frequency

### Timing Guidelines (Starting Point, Not Rules)

- **Day of week**: Tuesday, Wednesday, Thursday tend to outperform Monday and Friday.
- **Time of day**: 9-11am and 1-3pm in the recipient's local timezone.
- These are averages. Your list's behavior may differ. Test against your own data.
- For global lists: send in time-zone-adjusted batches or pick a single time that works
  for your primary market.

### Frequency Management

- Set a maximum send frequency per contact. More than one marketing email per week
  to the same contact increases unsubscribe and complaint rates unless engagement is
  consistently very high.
- High-frequency sending to low-engagement contacts is the fastest path to spam folder.
- More email is not more revenue if it degrades your deliverability.

### Suppressing Recent Converters

- Remove contacts from a campaign the moment they complete the campaign's goal.
- Sending "sign up for our trial" to someone who signed up yesterday is a quick
  unsubscribe.
- Most ESPs support exclusion segments or goal-based suppression at campaign level.

---

## Writing for Engagement

### Structure

1. **Hook** (first line): the one sentence that earns the rest of the read. Make the
   value or the problem immediately clear.
2. **Context**: why this email exists and why it matters to this reader right now.
3. **Body**: supporting detail, proof, or story. Keep it short.
4. **CTA**: one clear next step. Make it a button, not just a text link.
5. **Footer**: legal requirements, unsubscribe link, contact info.

### Tone

- Write to one person, not a crowd. "You" not "our customers".
- Match the register of your product and audience. B2B SaaS and consumer DTC have
  different voice expectations.
- Short sentences. Short paragraphs. White space is your friend.
- Read it aloud before sending. If it sounds like corporate announcement copy, rewrite it.

### CTA Copy

- Action verb first: "Start your trial", "Download the guide", "See the new features"
- Avoid: "Click here", "Learn more" (too generic), "Submit"
- The CTA should tell the reader exactly what will happen when they click.

### Reply Rate

Reply rate is one of the most valuable engagement signals in marketing email. A reply
moves you to the recipient's Primary inbox and trains their spam filter to trust you.
It is a stronger signal than a click or open.

Not every campaign should solicit replies, but where the goal is relationship-building
(welcome sequences, onboarding, re-engagement), designing for replies beats designing
for clicks:

- Use a real reply-to address, never `noreply@`
- End with a direct question rather than a CTA button: "What brought you to [Product]?
  Hit reply and tell me."
- Plain-text or minimal HTML gets more replies than a branded template

See `content/SKILL.md` for the full set of reply-inviting copy patterns.

---

## A/B Testing

### What to Test (in priority order)

1. Subject line (highest leverage, easiest to test)
2. Send time
3. CTA copy and button color
4. Email length (short vs. long)
5. Personalization (with vs. without)
6. Content angle (benefit-led vs. story-led)

### How to Test Properly

- Test one variable at a time.
- Use a statistically meaningful sample. For most lists: 10-20% for each variant,
  then send the winner to the remainder.
- Measure by **click rate** or **reply rate**, not open rate. Open rates are unreliable due to Apple MPP.
- Run tests consistently: same day of week, same time of day, comparable segments.
- Document results. Build a library of what has worked for your audience.

---

## Unsubscribe Experience

### Make It Easy

- One-click unsubscribe is required by Gmail and Yahoo for bulk senders as of 2024.
- Do not require login to unsubscribe.
- Do not make the recipient confirm their email address on the unsubscribe page.
- Do not add friction ("are you sure?", multi-step forms) to the unsubscribe process.
  Friction converts unsubscribers into spam reporters. Spam complaints hurt you far
  more than unsubscribes do.

### Preference Center (Optional but Valuable)

- Instead of a single unsubscribe, offer a preference center where contacts can choose
  which types of email they want.
- Example options: product updates, weekly digest, promotional offers, important account news.
- This reduces net unsubscribes while giving recipients genuine control.

### Processing Unsubscribes

- Honor unsubscribes immediately in your system, not just with your ESP.
- If you use multiple sending tools, sync suppression lists across all of them.
- CAN-SPAM allows up to 10 business days. Best practice is immediate.

---

## Campaign Deliverability Checklist

Before sending any marketing campaign:

- [ ] List segmented, not sending to full list indiscriminately
- [ ] Inactive contacts excluded or in separate re-engagement flow
- [ ] Suppression list applied (unsubscribes, hard bounces, complaints)
- [ ] Subject line reviewed for spam trigger words
- [ ] Preview text set explicitly
- [ ] Plain-text version included
- [ ] Single primary CTA with action-oriented label
- [ ] Unsubscribe link present, functional, and prominent
- [ ] Physical mailing address in footer
- [ ] No URL shorteners in links
- [ ] Large send batched or staggered
- [ ] Monitoring plan: will check complaint rate in first hour
