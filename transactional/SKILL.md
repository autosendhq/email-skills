---
name: transactional
description: >
  Best practices for designing, writing, and sending transactional email. Always use this
  skill for transactional email tasks; a password reset or OTP landing in spam is a
  product failure, not just a deliverability problem, so these emails deserve special care.
  Use it for: OTP and 2FA emails, password reset flows, email verification, order
  confirmations, receipts, shipping notifications, account and security alerts, billing
  emails, subscription events, or any user-triggered notification. Also load it when asked
  to diagnose why a transactional email is going to spam, or when deciding whether to add
  promotional content to a system-triggered email.
---

# Transactional Email

Transactional email is triggered by a user action. It is expected, often time-sensitive,
and carries higher deliverability expectations than marketing email because it is directly
tied to product functionality. A password reset landing in spam is a product failure.

> Related skills: `authentication/SKILL.md` (dedicated subdomain auth setup),
> `content/SKILL.md` (HTML structure, plain-text requirements),
> `compliance/SKILL.md` (transactional exemptions and their limits)

---

## What Counts as Transactional

Transactional email is triggered by a specific user action and contains information the
user needs as a result of that action. It is not initiated by the sender for commercial
purposes.

**Clearly transactional:**

- Email/account verification at signup
- Password reset links
- OTP / 2FA codes
- Order confirmations
- Receipts and invoices
- Shipping notifications and tracking updates
- Account alerts (login from new device, password changed, payment failed)
- Subscription renewal confirmations
- Waitlist or reservation confirmations

**Gray area (transactional with commercial risk):**

- Welcome emails (often have a transactional trigger but are used for onboarding/upsell)
- Trial expiry warnings (triggered by an event but primarily commercial in intent)
- Inactivity reminders (user-data-triggered but sender-initiated in purpose)

When in doubt: if removing the email would break a user's experience of the product,
it is transactional. If it primarily exists to drive revenue or retention, it is marketing.

**Why the distinction matters:**

- Transactional email is exempt from certain consent requirements (CAN-SPAM, GDPR)
- Mixing promotional content into transactional email can void the exemption
- Transactional and marketing must be on separate sending streams (different subdomains
  or IPs) so a spam complaint on marketing never affects transactional delivery

---

## Infrastructure for Transactional Email

### Dedicated Sending Stream

- Use a separate subdomain for transactional: `tx.yourdomain.com`, `mail.yourdomain.com`
- Separate from your marketing subdomain: `em.yourdomain.com`, `news.yourdomain.com`
- Configure separate SPF and DKIM records for each subdomain
- If using a shared IP from an ESP, confirm transactional and marketing traffic is
  separated at the IP or pool level

### Delivery Priority

- Configure your ESP or SMTP relay to prioritize transactional sends
- Use a dedicated API endpoint or separate subaccount for transactional if your provider
  supports it
- Do not batch transactional emails, send immediately on trigger

### No Rate Limiting for Time-Sensitive Emails

- OTPs, password resets, and login links must send with no delay
- Apply rate limiting at the product level (limit how many resets a user can request per
  hour) not at the sending level
- Queue management for transactional: FIFO with highest priority, no batching

---

## OTP and 2FA Emails

One-time passwords sent by email are security-critical. The email must arrive within
seconds and the code must be clearly readable.

### Content Structure

1. **The code, prominently.** Large font, centered, easy to copy. Do not bury it.
2. **Context.** What is this code for? Which product, which action.
3. **Expiry.** State clearly: "This code expires in 10 minutes."
4. **Security note.** "If you didn't request this, you can safely ignore this email.
   Your account has not been accessed."
5. **Support link.** If the user is confused, how do they get help?

### Code Requirements

- Expire codes after **10 minutes** maximum. 5 minutes is better for high-security contexts.
- Use cryptographically random codes, not sequential or time-based patterns that are
  guessable.
- **6 digits** is the standard. 8 digits for higher security contexts.
- Do not put the code in the email subject line. Subject lines are logged in many systems
  and visible in notification previews without opening the email.
- Invalidate the code after first use.
- Implement a rate limit: no more than 3-5 OTP requests per hour per account.

### Template Example Structure

```
Subject: Your [Product] verification code

[Product Logo]

Your verification code

[  123456  ]           ← Large, centered, easy to copy

This code expires in 10 minutes.

Enter this code in [Product] to [complete login / verify your email / etc.].

Didn't request this? You can safely ignore this email.
Your account remains secure.

[Contact Support]
```

### Plain-Text Version

```
Your [Product] verification code: 123456

This code expires in 10 minutes.

Enter this code in [Product] to [action].

Didn't request this? You can safely ignore this email.

Support: https://yourdomain.com/support
```

---

## Password Reset Emails

### Content Requirements

1. **The reset link, immediately visible.** Do not make the user scroll.
2. **Expiry time.** State clearly in the email body: "This link expires in 60 minutes."
3. **Security acknowledgment.** "If you didn't request this, no action is needed. Your
   password has not been changed."
4. **Single-use.** The link must be invalidated after use.
5. **IP or device hint (optional but adds trust).** "This was requested from IP 203.x.x.x"

### Link Expiry

- Password reset links: **60 minutes** maximum
- For sensitive actions (account deletion, payment method change): **15-30 minutes**
- State the expiry in the email, if a user comes back 2 hours later and the link is
  expired, they should have been warned

### Do Not Log Tokens in Subject Lines

The subject line `"Here is your password reset link: https://..."` exposes the token
in email logs, preview notifications, and push notification systems. Keep the token
in the body only.

### Template Example Structure

```
Subject: Reset your [Product] password

[Product Logo]

Reset your password

Someone requested a password reset for your [Product] account.

[Reset Password]          ← Prominent button

This link expires in 60 minutes.

If you didn't request this, you can safely ignore this email.
Your password will not change.

If you're having trouble with the button, copy and paste this link:
https://yourdomain.com/reset?token=[token]

[Contact Support]
```

---

## Email Verification

Sent when a new user signs up or when they update their email address. This is the
clearest case of a fully transactional email.

### Content Requirements

1. **Clear purpose.** "Verify your email address to activate your account."
2. **Single, prominent CTA.** One button. No distractions.
3. **Fallback link.** Always include the URL below the button for clients that block HTML.
4. **No promotional content.** Do not use this email for onboarding upsell.
5. **Expiry.** Verification links should expire after 24-48 hours.

### If Verification Is Not Completed

- Send one reminder after 24 hours if the link has not been clicked
- Do not send additional marketing or onboarding email until verification is complete
- After 72 hours of no verification, the account can be flagged as unverified

---

## Order Confirmations and Receipts

### Required Information

Every order confirmation must include:

- Order ID or reference number
- Summary of what was ordered (item names, quantities)
- Unit prices and total
- Billing address (or confirmation it matches the account)
- Shipping address (if physical goods)
- Estimated delivery date or timeframe
- Link to order status or tracking
- Customer support contact

### Receipts (Digital Products / Services)

- Invoice number
- Billing period (for subscriptions)
- Line items with amounts
- Total charged
- Payment method (last 4 digits only, never full card number)
- Link to download the receipt as PDF if needed for business expenses

### Subject Line Patterns That Work

- "Your order #12345 is confirmed"
- "Receipt for $49 - [Product Name]"
- "Order confirmed, ships Thursday"

### Do Not Include Promotional Content Above the Fold

A receipt is the highest-trust transactional email you send. Cluttering it with upsell
above the order details breaks trust and may void the transactional exemption if the
promotional content is substantial.

A subtle product recommendation below the order details is acceptable. A prominent
banner above the receipt content is not.

---

## Shipping and Delivery Notifications

### Trigger Points

Send a notification at each of these events (based on what your fulfillment system supports):

1. Order received and processing
2. Order shipped (include tracking number and carrier)
3. Out for delivery (if carrier data supports it)
4. Delivered
5. Delivery exception (delay, failed delivery attempt). This one is often skipped but
   has the highest value to customers

### Content for Each Stage

**Shipped:**

- Carrier name and tracking number
- Direct tracking link (deep link into carrier tracking, not just the carrier homepage)
- Estimated delivery date
- Items in this shipment (especially if split shipments)

**Delivered:**

- Confirmation of delivery time
- Link to order details in case there is a problem
- Clear path to report an issue (damaged, missing item)

### Subject Lines

- "Your order has shipped - arrives Thursday"
- "Delivered: Order #12345"
- "Heads up: your delivery was attempted today"

---

## Account and Security Alerts

These are the most important transactional emails for user trust. They must always reach
the inbox.

### When to Send

- New login from an unrecognized device or location
- Password successfully changed
- Email address changed (send to both old and new address)
- Payment method added or changed
- Subscription plan changed
- Account deletion requested (with cancellation link)
- Failed payment attempt

### Content Requirements

- State what happened, clearly and immediately.
- Include relevant context: device type, location, time, IP (where applicable).
- Provide a clear action if the user did not authorize the change: "If this wasn't you,
  [secure your account]."
- Link to account security settings.
- Do not require login to read the email, the security alert is useful even if the
  user's account has been compromised.

### Tone

- Factual, not alarming. "A new device signed into your account" not
  "SECURITY ALERT: Your account may be compromised."
- Alarming subject lines increase stress and, ironically, increase spam complaints
  from users who mistake the email for a phishing attempt.
- Match your brand voice but stay matter-of-fact on security alerts.

---

## Billing and Subscription Emails

### Upcoming Renewal Notice

- Send 7 days before renewal for monthly plans; 30 days for annual plans.
- Include: renewal date, amount, payment method on file.
- Link to manage subscription or payment method.
- If the plan includes a price change, this must be disclosed clearly and with adequate
  notice (30+ days; check local regulations).

### Payment Failed

- Send immediately on payment failure.
- Include: what failed, which card was charged, the amount.
- Clear CTA to update payment method.
- State the retry schedule: "We'll try again in 3 days."
- Follow up before service suspension: "Your account will be paused in 24 hours."

### Subscription Cancelled

- Confirmation of cancellation.
- Effective date (especially for billing-period-end cancellations).
- What the user retains access to and until when.
- A single, non-pushy option to reactivate.

---

## Promotional Content in Transactional Email

Adding promotional content to transactional email is a gray area with legal and
deliverability implications.

### When It Is Acceptable

- Below the fold, clearly separated from the transactional content
- Minor and clearly secondary to the transactional purpose
- Does not change the primary nature of the email
- Example: a receipt with a small "You might also like" section at the bottom

### When It Is Not Acceptable

- Promotional content is above the fold or visually dominant
- The transactional content is minimal and the promotion is the real purpose
- The email is sent to contacts who have unsubscribed from marketing
- Example: an "order confirmed" email with a large promotional banner above the order
  details, or a minimal "your account is ready" email that is primarily an upsell

### The Compliance Risk

If the promotional content is the primary purpose of the email, regulators may classify
the email as commercial rather than transactional, applying full consent and opt-out
requirements. Keep promotions genuinely incidental.

---

## Transactional Email Checklist

Before deploying a transactional email flow:

- [ ] Sending from a dedicated subdomain separate from marketing
- [ ] SPF and DKIM configured for the transactional subdomain
- [ ] Send triggered immediately on user action (no batch delay)
- [ ] OTP codes expire within 10 minutes; reset links within 60 minutes
- [ ] Tokens and codes are not in the subject line
- [ ] Plain-text version included alongside HTML
- [ ] All links are full HTTPS URLs (no shorteners)
- [ ] "If you didn't request this" message included on security emails
- [ ] No promotional content above the fold
- [ ] Support contact or link included
- [ ] Tested in Gmail, Outlook, and Apple Mail
- [ ] Spam score checked (mail-tester.com) before deployment
