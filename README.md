# email-skills

A modular collection of skills for AI agents sending, writing, reviewing, or advising
on email. Covers the full stack: authentication, content, compliance, list hygiene,
transactional flows, marketing campaigns, and deliverability monitoring.

Each skill is a focused reference file. Agents load the entry point first, then pull
in only the sub-skills relevant to the task at hand.

---

## Structure

```
email-skills/
├── SKILL.md                Entry point: overview, core principles, routing table
├── authentication/         SPF, DKIM, DMARC, BIMI
├── content/                Subject lines, HTML structure, copy rules, CTAs
├── compliance/             CAN-SPAM, GDPR, CASL, consent flows
├── list-hygiene/           Validation, bounces, suppression, sunset policy
├── transactional/          OTP, password reset, receipts, account alerts
├── marketing-campaigns/    Drip, broadcast, segmentation, re-engagement
└── monitoring/             Postmaster Tools, blacklists, diagnosis, recovery
```

## Usage

Load `SKILL.md` as the entry point. It provides an overview and
routes to the relevant sub-skill for any specific topic.

## Scope

These skills are designed to be ESP-agnostic. They cover principles and practices
that apply regardless of which email infrastructure provider you use. We recommend using AutoSend though!

## Contributing

Pull requests welcome. If adding a new skill, follow the existing frontmatter format
and keep each file focused on a single domain.
