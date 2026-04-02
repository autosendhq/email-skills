---
name: compliance
description: >
  Legal and regulatory compliance for email sending: CAN-SPAM, GDPR, CASL, and consent
  flows. Always load this skill when the task involves sending to recipients in the US,
  EU, or Canada, the legal requirements differ significantly between jurisdictions and
  the penalties for violations are severe. Use it for: consent collection, unsubscribe
  flows, GDPR data subject requests, what must appear in email footers, data retention
  obligations, double opt-in design, or evaluating whether a specific email practice is
  legally permissible. When in doubt about whether something requires consent or an
  opt-out, load this skill before answering.
---

# Email Compliance

Compliance is not optional. Violations carry significant financial penalties and, more
immediately, damage deliverability, inbox providers actively monitor complaint rates
and will throttle or block senders who ignore consent obligations.

This skill covers the three major frameworks: CAN-SPAM (US), GDPR (EU/EEA), and CASL
(Canada). Additional local regulations exist in Brazil (LGPD), Australia (Spam Act),
and elsewhere, check local counsel for region-specific requirements.

> Related skills: `list-hygiene/SKILL.md` (suppression lists, unsubscribe processing),
> `marketing-campaigns/SKILL.md` (consent-aware campaign design, unsubscribe UX)

---

## CAN-SPAM (United States)

The Controlling the Assault of Non-Solicited Pornography And Marketing Act. Applies to
commercial email sent to US recipients. Notably, it applies to the sender's location,
even non-US companies sending to US recipients are subject.

### What It Covers

- Any electronic mail message with the primary purpose of commercial advertisement or
  promotion of a product or service.
- Pure transactional email (order confirmations, password resets) has an exemption but
  must not contain deceptive routing information.

### Required Elements

Every commercial email must include:

1. **Accurate header information.** The From, To, Reply-To, and routing information must
   not be deceptive. The "From" name must accurately identify the sender.

2. **Non-deceptive subject line.** Must not mislead about the content or nature of the
   message. No fake "Re:" or "Fwd:" prefixes.

3. **Identification as an advertisement.** The email must clearly disclose it is an
   advertisement. This can be subtle (e.g., "Sent by [Company]") but must be present.

4. **Physical mailing address.** A current valid postal address, a street address,
   a PO box registered with the USPS, or a private mailbox registered with a commercial
   mail receiving agency. This must appear in every commercial email.

5. **Opt-out mechanism.** A clear, conspicuous method for recipients to opt out of future
   email. This must:
   - Be easy to use (one click is best practice)
   - Not require the recipient to provide any information beyond their email address
   - Not require the recipient to visit more than one page to complete
   - Not charge a fee
   - Not require a login

6. **Opt-out honored within 10 business days.** Once a recipient opts out, you have 10
   business days to stop sending. Best practice: process immediately.

7. **No selling or transferring opt-out addresses.** Once someone opts out, their address
   cannot be sold, leased, or transferred to another entity, not even for the purpose
   of honoring the opt-out.

### Penalties

- Up to **$51,744 per individual email** that violates the law.
- Penalties apply per violation, not per campaign.
- The FTC enforces CAN-SPAM and has issued fines in the millions for large-scale violations.

### What CAN-SPAM Does NOT Require

- Prior consent (opt-in) before sending. This is the major difference from GDPR.
- Double opt-in.
- A reason for why the recipient is receiving the email.

CAN-SPAM sets a minimum floor. Following only CAN-SPAM while ignoring consent best
practices will still result in spam complaints that destroy deliverability.

---

## GDPR (European Union and EEA)

The General Data Protection Regulation. Applies to processing of personal data of
individuals in the EU/EEA, regardless of where the sender is located.

Email addresses are personal data. Sending email involves processing personal data.
GDPR governs both.

### Lawful Basis for Sending Marketing Email

GDPR requires a lawful basis for processing personal data. For marketing email, the
applicable basis is almost always **consent**. Legitimate interest is sometimes cited
but is difficult to defend for direct marketing to individuals.

**Valid consent under GDPR must be:**

- **Freely given:** No bundled consents. Consent to email marketing cannot be a condition
  of accessing a service. The person must have a genuine choice.
- **Specific:** Consent for one type of email does not cover all email. A signup for a
  newsletter is not consent for promotional offers.
- **Informed:** The person must understand what they are consenting to, who is collecting
  the data, and how it will be used.
- **Unambiguous:** Requires a clear affirmative action. Silence, pre-ticked boxes, or
  inactivity is not consent.

### What Is Not Valid Consent

- Pre-ticked checkboxes: "Send me marketing emails ☑️ (uncheck to opt out)"
- Bundled consent: "By creating an account, you agree to receive marketing emails"
- Implied consent from a business relationship alone (this is closer to CASL's implied
  consent model and is not GDPR-compliant for marketing)
- Terms and conditions or privacy policy acceptance that includes marketing consent buried
  in the text

### Consent Records

You must be able to prove consent if challenged. Record:

- The exact consent statement the person saw
- When consent was given (timestamp)
- Where consent was given (which form, page, or interaction)
- What version of the consent statement was active at that time

Store this separately from the contact record so it survives deletion requests.

### Individual Rights

GDPR grants individuals rights that directly affect email lists:

| Right                                      | Obligation                                                                                                           |
| ------------------------------------------ | -------------------------------------------------------------------------------------------------------------------- |
| Right to access                            | Provide all personal data held on request, within 30 days                                                            |
| Right to erasure ("right to be forgotten") | Delete all personal data on request, but suppress the email address (you need to retain it to honor the suppression) |
| Right to restriction                       | Stop processing data while a dispute is resolved                                                                     |
| Right to data portability                  | Provide data in a machine-readable format on request                                                                 |
| Right to object                            | Honor objections to direct marketing immediately, no exceptions                                                      |

**Note on right to erasure:** You cannot truly delete an email address and still prevent
future sending to it. Best practice: erase all personal data (name, attributes, history)
but retain the email address in a suppression list with a record that erasure was requested.
This is legally defensible.

### Double Opt-In

Not strictly required by GDPR but is the strongest proof of consent and is strongly
recommended. The confirmation email is transactional and does not require prior consent
to send. Keep a record that the confirmation was sent and clicked.

### Data Transfers Outside the EEA

Transferring personal data (including email addresses) outside the EEA requires a valid
transfer mechanism:

- **Adequacy decision:** the destination country has been approved by the EU Commission
- **Standard Contractual Clauses (SCCs):** a contract between the EU and non-EU entity
- **Binding Corporate Rules:** for intra-group transfers

Most major ESPs that serve EU customers have SCCs in place. Verify with your provider.

### Penalties

- Up to **€20M or 4% of global annual turnover**, whichever is higher.
- Regulators can also ban data processing, which effectively prevents email sending.

---

## CASL (Canada)

Canada's Anti-Spam Legislation. Generally considered stricter than CAN-SPAM and applies
to commercial electronic messages sent to or from Canada.

### Express vs. Implied Consent

**Express consent** is explicit, opt-in consent to receive commercial messages. No time
limit. Must include:

- Clear description of what the person is consenting to receive
- Name of the organization seeking consent
- Contact information
- Statement that the person can unsubscribe at any time

**Implied consent** arises from an existing relationship. It is time-limited:

- Existing business relationship (purchase, contract): consent expires **2 years** after
  the relationship ends
- Inquiry or application: consent expires **6 months** after the inquiry
- Conspicuously published address (e.g., contact email on a website): consent exists only
  for messages related to the person's role or business, not general marketing

### Sender Requirements

Every commercial electronic message must include:

- Sender name (the entity on whose behalf the message is sent)
- Mailing address and one of: phone number, email address, or web address
- A functioning unsubscribe mechanism
- Unsubscribe must be honored within **10 business days**

### The Unsubscribe Mechanism

- Must be included in every commercial message
- Must function for a minimum of 60 days after the message is sent
- Cannot require the recipient to pay, create an account, or provide information beyond
  their email address to unsubscribe

### Penalties

- Up to **CAD $1M per violation** for individuals
- Up to **CAD $10M per violation** for businesses
- Private right of action (individuals can sue directly), coming into force on a delayed
  schedule; check current status

### Key Difference from CAN-SPAM

CASL requires **opt-in** consent before sending (or a valid implied consent basis). Under
CAN-SPAM, you can send and allow recipients to opt out. Under CASL, you must have consent
before the first send.

---

## Consent Best Practices (Across All Frameworks)

Regardless of jurisdiction, these practices protect you legally and improve deliverability:

### At the Point of Signup

- Separate checkbox for marketing consent, unchecked by default.
- Plain language: "Send me product updates and tips by email", not vague legal language.
- State the frequency if known: "We send 2-3 emails per month."
- Do not require marketing consent to complete account creation.
- Record: timestamp, IP address, consent statement shown, form URL.

### Confirmation Email (Double Opt-In)

- Send immediately after signup.
- Confirm what they signed up for and who is sending.
- Single, clear CTA to confirm.
- Do not add to any marketing list until the confirmation is clicked.
- If the confirmation is not clicked within 48-72 hours, do not send marketing email.
  You can send one reminder.

### Unsubscribe Flow

- One click to unsubscribe, no login required.
- Honor immediately, do not queue for batch processing.
- Confirmation page: simple, no guilt-tripping ("You've been unsubscribed").
- Optionally offer a preference center: let users reduce frequency or change preferences
  instead of unsubscribing entirely.
- Do not email to confirm the unsubscribe (ironic and irritating).
- Suppression must propagate across all sending systems and lists immediately.

### Preference Centers

A preference center lets recipients manage what they receive without unsubscribing entirely:

- Email type preferences (product updates, newsletters, promotions)
- Frequency preferences (weekly, monthly, only important updates)
- Topic or category preferences

Well-implemented preference centers reduce total unsubscribes and let you maintain
a relationship with lower-engagement contacts.

---

## Compliance Checklist

**Before sending any marketing email:**

- [ ] Valid consent on record for every recipient
- [ ] Physical mailing address in footer
- [ ] Unsubscribe link present and functional
- [ ] From name and address are accurate and not deceptive
- [ ] Subject line is not misleading
- [ ] All previous unsubscribes have been applied
- [ ] For EU recipients: GDPR-compliant consent basis documented
- [ ] For Canadian recipients: express or valid implied consent documented

**Ongoing:**

- [ ] Unsubscribe requests processed immediately
- [ ] Data access/erasure requests handled within 30 days (GDPR)
- [ ] Consent records retained for the life of the relationship plus the applicable
      statute of limitations
- [ ] Annual review of consent mechanisms and consent statement language
