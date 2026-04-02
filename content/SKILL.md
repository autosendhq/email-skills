---
name: content
description: >
  Best practices for writing email content that avoids spam filters and gets read. Load
  this skill whenever writing or reviewing any email from scratch - content decisions
  affect deliverability just as much as authentication does. Use it for: composing subject
  lines, preview text, HTML email structure, body copy, CTAs, plain-text alternatives,
  image guidelines, link rules, or auditing an email for spam signals. Also load it when
  a user asks why a specific email feels spammy, what words to avoid, how to structure an
  HTML email, or how to write a CTA. Covers both transactional and marketing content.
---

# Email Content

Content is the layer most visible to recipients and to spam filters. A technically
sound setup (authentication, list hygiene) can still fail if the content looks like spam.
This skill covers everything between the sending server and the reader's eyes.

> Related skills: `authentication/SKILL.md` (technical foundation), `compliance/SKILL.md`
> (CAN-SPAM required footer elements), `transactional/SKILL.md` (transactional-specific
> content patterns), `marketing-campaigns/SKILL.md` (campaign copywriting and structure)

---

## Subject Lines

The subject line determines whether the email gets opened or marked as spam before
being opened. It also carries deliverability weight: spam filters evaluate subject lines
independently from body content.

### Length

- Keep under **60 characters** to avoid truncation in most clients.
- Mobile shows even less: aim for the key message in the first **40 characters**.
- Front-load the most important word or phrase. Do not bury it after "Re:" or "Update:".

### Patterns That Trigger Spam Filters

Avoid these in subject lines (and body copy):

**Financial / urgency language:**

- Free, FREE, f.r.e.e (deliberate misspelling doesn't help)
- Cash, earn, income, profit, make money, extra income
- Risk-free, no risk, guaranteed, 100% free
- Act now, urgent, limited time, expires, deadline, last chance
- Winner, you've been selected, you've won, claim your prize

**Clickbait / deceptive patterns:**

- Re: (when it is not actually a reply)
- Fwd: (when it is not actually a forward)
- You have been chosen
- Congratulations (in commercial context)
- This is not spam

**Formatting red flags:**

- ALL CAPS words or entire subject lines
- Excessive punctuation: `!!!`, `???`, `$$$`
- Multiple exclamation marks
- Emoji overload (1-2 relevant emoji is fine; a row of them is not)
- Dollar signs, especially multiple: `$$$`

### What Works

- **Specificity:** "Your order ships Thursday" beats "Update on your order"
- **Personalization:** First name, account detail, or product they use
- **Curiosity gap:** Leave something to discover, but do not be misleading
- **Plain and direct for transactional:** "Your password reset link", "Receipt for $49"
- **Questions:** Work well for marketing email - "Is your email landing in spam?"
- **Numbers:** "5 things that hurt deliverability" - concrete and skimmable

### Testing Subject Lines

- Run through a spam checker before sending (mail-tester.com scores subject lines).
- A/B test subject lines on a subset before full send. Even a 10% sample gives signal.
- Track spam complaint rate by subject line variation - a spike often points to the subject.

---

## Preview Text

Preview text is the grey text that appears after the subject line in the inbox view. It
is the second thing a recipient reads before deciding to open. It also appears as the
description in mobile push notifications and lock screen alerts, so it does double duty.

### Rules

- Set it explicitly. If you do not, email clients pull the first readable text from the
  body - often "View this email in your browser" or an image alt tag.
- Keep it under **100 characters**.
- Write it as a continuation of the subject line, not a repeat.
  - Subject: "Your September invoice is ready"
  - Preview: "Amount due: $120 - payment due Oct 1"
- Do not include the unsubscribe link text or footer content (it shows up if the body is
  short and there is no explicit preheader).

### HTML Implementation

Add hidden preheader text immediately after the opening `<body>` tag:

```html
<div style="display:none;max-height:0;overflow:hidden;mso-hide:all;">
	Your preview text here. Keep it under 100 characters.
</div>
<!-- Add a spacer to prevent the next visible text from also showing as preview -->
<div style="display:none;max-height:0;overflow:hidden;mso-hide:all;">
	&nbsp;&zwnj;&nbsp;&zwnj;&nbsp;&zwnj;&nbsp;&zwnj;&nbsp;&zwnj;
</div>
```

The spacer (repeated zero-width non-joiners) fills up the preview space so body content
does not bleed through.

---

## HTML Email Structure

### Required: Plain-Text Alternative

Every HTML email must include a `text/plain` version in a `multipart/alternative` MIME
structure. Emails without it are treated as suspicious by many spam filters and fail
accessibility requirements.

The plain-text version should:

- Contain all the information in the HTML version
- Use line breaks, spacing, and ASCII characters for structure
- Include all links spelled out in full: `https://yourdomain.com/reset?token=...`
- Not be a blank or stub - spam filters detect placeholder plain-text

Most ESPs generate plain-text automatically but do a poor job. Review and edit it manually
for important sends.

### HTML Best Practices

**Structure:**

- Use a single-column layout at 600px wide. This renders correctly across all clients.
- Use `<table>` based layouts for maximum Outlook compatibility. CSS flexbox and grid
  are not reliably supported in email clients.
- Maximum width: 600-700px. Wider layouts break on mobile.
- Mobile-responsive: use `@media` queries and fluid widths. Over 60% of email is opened
  on mobile.

**CSS:**

- Use inline CSS for all critical styling. Gmail and many other clients strip `<head>`
  and `<style>` blocks.
- Avoid shorthand CSS properties in some clients - spell out `padding-top`, `padding-right`,
  etc. individually.
- Safe font stack: `Arial, Helvetica, sans-serif` or `Georgia, serif`. Web fonts
  (Google Fonts) are not supported in Outlook or Gmail. System fonts like
  `-apple-system, BlinkMacSystemFont, "Segoe UI"` can be used as progressive
  enhancement: Apple Mail and iOS Mail render `-apple-system`, Outlook on Windows
  renders `Segoe UI`, but Outlook's Word engine ignores `-apple-system` and falls
  back to the next font in the stack. Always end with `Arial` or `Helvetica` as
  the guaranteed fallback: `-apple-system, BlinkMacSystemFont, "Segoe UI", Arial, sans-serif`.
- Do not use `position: absolute/fixed`, `z-index`, `float`, or JavaScript. They are
  stripped or ignored.

**JavaScript in email:** Avoid it entirely. Every major email client strips or sandboxes
script tags, so JavaScript never executes in email - but its presence is still detected
by spam filters as a signal of bulk or malicious mail. There is no legitimate use case
for JavaScript in email that is not better served by a link to a web page.

**Dark mode support:**

```css
@media (prefers-color-scheme: dark) {
	body {
		background-color: #1a1a1a !important;
	}
	.email-body {
		background-color: #2d2d2d !important;
		color: #ffffff !important;
	}
}
```

Test in Apple Mail dark mode and the Gmail dark mode implementation (they behave differently).

**Accessibility:**

- `lang` attribute on the `<html>` tag: `<html lang="en">`
- `role="presentation"` on layout tables
- `alt` text on every `<img>` tag - including decorative images (use `alt=""` for decorative)
- Sufficient color contrast: minimum 4.5:1 ratio for body text
- Do not rely on color alone to convey information

### Image Guidelines

**Image-to-text ratio:** At least 60% of the email's content area should be text.
Emails composed primarily or entirely of images are a near-certain spam trigger. Spam
filters cannot read image content - an image-only email looks like an attempt to hide
content from the filter.

**Hosting:** Host images on your own CDN or your ESP's image hosting. Third-party free
image hosts (Imgur, etc.) carry reputation baggage from other users.

**File formats:**

- PNG: best for logos, illustrations, and images with transparency
- JPEG: best for photographs
- GIF: acceptable for simple animations; keep file size under 1MB
- WebP: not universally supported in email clients - use PNG or JPEG

**File size:** Keep the total email size under 102KB of HTML. Gmail clips emails over
this size and shows a "View entire message" link, which cuts off your tracking pixel
and CTA.

**Alt text:**

- Informational images: descriptive alt text that conveys the same information
- CTA images (buttons as images): alt text should match the button label
- Decorative images: `alt=""`
- Never use alt text as a secondary content delivery mechanism

**Retina/HiDPI:** Use images at 2x the displayed size and constrain with `width` attribute:

```html
<img src="logo@2x.png" width="200" alt="Company Logo" />
```

---

## Links

### What to Avoid

- **URL shorteners** (bit.ly, tinyurl, ow.ly, etc.): widely used by spammers; flagged by
  spam filters. Never use them in email.
- **Mismatched display URLs and actual URLs:** spam filters check this. `<a href="https://phishing.com">Click here to visit trusted.com</a>` is flagged immediately.
- **Bare IP addresses in links:** `http://203.0.113.0/page` looks like a phishing link.
- **Too many links:** a wall of links resembles a phishing email. Keep to what is necessary.
- **Linking to low-reputation domains:** every domain in your links carries its own reputation.
  A link to a blacklisted or suspicious domain will damage your email's deliverability.

### Click Tracking

- If you use click tracking, use a custom tracking domain aligned with your sending domain
  (e.g., `click.yourdomain.com`).
- Generic ESP tracking domains (`click.sendingprovider.com`) are shared across all that
  provider's customers and can carry reputation baggage.
- Tracked links should redirect cleanly. Chains of redirects (3+) are a spam signal.

### Link Best Practices

- Make link text descriptive: "Reset your password" not "click here"
- Use full HTTPS URLs - `http://` links are a spam and trust signal problem
- The unsubscribe link should be a one-click process - no login required

---

## Body Copy

### Spam Trigger Words (Body)

Beyond subject lines, avoid these in the body where possible:

- Financial: "consolidate debt", "refinance", "mortgage rates", "home equity", "payday"
- Medical: "lose weight", "burn fat", "fast weight loss", "miracle", "as seen on TV"
- Ecommerce spam: "buy direct", "order now", "retail value", "savings", "drastically reduced"
- Trust overreach: "this is not spam", "100% satisfied", "no strings attached", "no questions asked"
- Action pressure: "don't delete", "open immediately", "read this now", "time sensitive"

Context matters. Some of these words are unavoidable in legitimate emails. The spam filter
evaluates them alongside other signals - a single instance in an otherwise clean email from
a reputable domain is unlikely to cause failure. A cluster of them is a problem.

### Formatting

- Keep paragraphs short: 2-4 lines maximum.
- Use one clear, prominent CTA. Multiple competing CTAs dilute engagement and look spammy.
- CTA button text should be action-oriented: "Start your trial" not "Click here" or "Submit".
- Use sufficient padding around clickable elements for mobile (minimum 44px tap target).
- Avoid excessive bolding, large fonts, or bright red text throughout the body.
- Never use white text on a white background or any other method of hiding content.
  Spam filters detect this, and it is illegal under CAN-SPAM.

### Encouraging Replies

A reply is the strongest positive signal a recipient can send to a spam filter: it moves
you to Primary in Gmail and trains the recipient's filter to trust you. No click or open
carries the same weight.

**Why replies happen:** Heavily designed HTML emails with CTA buttons signal "broadcast."
Recipients do not reply to broadcasts. Plain or plain-looking emails that read like they
came from a real person invite replies because they feel like a conversation.

- **Use a real reply-to address.** `noreply@` closes the conversation before it starts.
- **End with a direct question.** The reply ask belongs at the end, not buried mid-body:
  "What's the biggest obstacle you're running into? Hit reply and let me know."
- **Ask for something small.** "Reply with one word" or "reply yes/no" reduces friction.
  A short reply counts the same as a long one.
- **Say you read replies**, but only if it is true. "I read every reply" signals a human
  is on the other end.
- **Match the format to the ask.** Plain-text or minimal HTML gets more replies than the
  same content in a branded template. Heavy design signals marketing, not conversation.
- **Do not mix a reply ask with competing CTAs.** Pick one.

This applies most to: outreach, feedback emails, onboarding emails from a founder or team
member, and re-engagement sequences. For transactional emails, reply rate is not the goal.

---

### The Footer

Every marketing email footer must include:

1. Physical mailing address (street address or PO box - CAN-SPAM requirement)
2. Unsubscribe link (one-click, no login)
3. Company name
4. Optionally: reason the recipient is receiving this email ("You're receiving this because
   you signed up at yourdomain.com on Jan 1, 2024")

Transactional emails should include at minimum a way to contact support if the email was
unexpected.

---

## Personalization and Dynamic Variables

Personalization improves engagement and signals relevance to spam filters - a recipient
who opens and clicks is a stronger reputation signal than one who ignores or deletes.
The mechanism for personalization is dynamic variables: placeholders in your template
that get replaced with contact-specific values at send time.

### Variable Syntax

The most widely used syntax, including in AutoSend, uses double curly braces:

```
{{first_name}}
{{company}}
{{plan_type}}
{{trial_end_date}}
```

These are replaced server-side before the email is sent. The recipient sees the resolved
value; the variable syntax never appears in the delivered email.

Other syntaxes exist across platforms:

```
{first_name}          → single brace (some platforms)
*|FNAME|*             → Mailchimp legacy
%first_name%          → older ESP formats
```

If you are writing templates for AutoSend, use `{{variable_name}}` throughout.

### Always Set Fallback Values

A missing variable with no fallback renders the raw placeholder in the email:
`Hi {{first_name}},` becomes `Hi ,` - or worse, `Hi {{first_name}},` - both of which
are immediately visible to the recipient and mark the email as a bulk send gone wrong.

Set a fallback for every variable that might be empty:

```
{{first_name | there}}       → "Hi there" if first_name is missing
{{company | your team}}      → "your team" if company is missing
{{plan_type | free plan}}    → "free plan" if plan_type is missing
```

The exact fallback syntax depends on your platform. In AutoSend and most Handlebars-style
systems, the pipe `|` character separates the variable from its fallback value.

### Where to Use Variables

**Subject line** - first name personalization in the subject line is one of the
highest-leverage uses. It increases open rate and reduces the likelihood of the email
feeling like a mass blast:

```
Hi {{first_name}}, your trial ends in 3 days
```

**Opening line** - use the first name or a relevant attribute to open the email body.
Avoid starting with "Dear {{first_name}}" - it reads as formal to the point of being
cold. "Hi {{first_name}}," is the standard.

**Dynamic content blocks** - for more advanced personalization, swap out entire sections
based on a contact property:

```
{{#if plan_type == "free"}}
  You're on the free plan. Here's what you'd unlock on Pro.
{{else}}
  Thanks for being a Pro subscriber.
{{/if}}
```

This keeps one template but delivers a meaningfully different email to different segments.

### Custom Fields

Beyond standard fields (first name, email, company), define custom fields that map to
how your product or audience is segmented:

- `{{industry}}` - tailor examples to the reader's context
- `{{last_active_feature}}` - reference what they've used in the product
- `{{account_id}}` - useful in transactional email for support reference
- `{{renewal_date}}` - billing and subscription emails
- `{{referral_code}}` - personalized referral links

Collect only what you will actually use. A field that is empty for 60% of your list
is a fallback problem waiting to happen.

### Testing Personalized Templates

Before sending to a live list, always send a test to yourself with:

- A contact record where all variables are populated (the happy path)
- A contact record where optional variables are empty (the fallback path)
- Verify both render correctly in Gmail, Apple Mail, and Outlook

Broken personalization in the subject line is one of the most visible bulk-email
mistakes - and the hardest to recover from once sent.

---

## CAN-SPAM Required Elements

For any commercial email (not purely transactional):

1. **Accurate From information:** sender name and address must not be deceptive.
2. **Accurate subject line:** must not mislead about the content.
3. **Physical address:** a valid postal address for the sending organization.
4. **Opt-out mechanism:** clear and conspicuous unsubscribe method.
5. **Opt-out honored within 10 business days.** Best practice: immediate.
6. **Clear identification as an advertisement** if the email is primarily commercial.
7. **No selling or transferring email addresses** after an opt-out.

---

## Email Client Compatibility Testing

Before deploying a new template, test rendering across:

**Priority clients (by market share):**

1. Apple Mail (iPhone, Mac)
2. Gmail (web, Android, iOS)
3. Outlook (2016, 2019, 365 on Windows - each behaves differently)
4. Yahoo Mail
5. Outlook.com (web)

**Testing tools:**

- **Litmus** or **Email on Acid:** render previews across 100+ clients
- **mail-tester.com:** spam score check with SPF/DKIM/content breakdown
- **Gmail** and a real iOS device: most important to test manually

**Common Outlook rendering issues:**

- Outlook uses Microsoft Word's rendering engine, not a browser engine. Many modern CSS
  properties do not work.
- Use `<!--[if mso]>` conditional comments for Outlook-specific overrides.
- Background images require VML fallback for Outlook:

```html
<!--[if gte mso 9]>
<v:rect xmlns:v="urn:schemas-microsoft-com:vml" fill="true" stroke="false">
  <v:fill type="tile" src="background.jpg" color="#ffffff"/>
  <v:textbox inset="0,0,0,0">
<![endif]-->
[your content here]
<!--[if gte mso 9]></v:textbox></v:rect><![endif]-->
```

---

## Open Tracking Considerations

Open tracking works by embedding a 1x1 transparent pixel image. When it loads, an open
is recorded. This mechanism has significant limitations:

- **Apple Mail Privacy Protection (MPP):** Since iOS 15 / macOS Monterey (2021), Apple
  pre-fetches all images in emails regardless of whether the email is actually opened.
  This inflates open rates significantly for any list with Apple Mail users.
- **Corporate email clients:** Many enterprise setups have security scanners that pre-fetch
  images, also inflating opens.
- **Spam filter interaction:** Some spam filters treat tracking pixels as a signal of
  bulk commercial email. In certain contexts, disabling open tracking can improve inbox
  placement.
- **Privacy regulations:** Some interpretations of GDPR consider tracking pixels to require
  consent.

**Recommendation:** Do not optimize for open rate as a primary performance metric.
Use click rate, reply rate, conversion rate, and revenue per email instead. Consider
whether the marginal data from open tracking outweighs the potential deliverability cost -
some email infrastructure providers disable open tracking by default for this reason.
