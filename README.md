# Email Skills

A modular collection of skills for AI agents sending, writing, reviewing, or advising
on email. Covers the full stack: authentication, content, compliance, list hygiene,
transactional flows, marketing campaigns, and deliverability monitoring.

Built by the team at [AutoSend](https://autosend.com). The content is ESP-agnostic and the principles apply regardless of which email infrastructure provider you use.

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
├── email-automation/       Onboarding sequences, lifecycle automation, triggers
└── monitoring/             Postmaster Tools, blacklists, diagnosis, recovery
```

## Installation

**To install using `skills` CLI:**

```bash
npx skills add autosendhq/email-skills
```

**Global (all projects):**

```bash
git clone https://github.com/akashbhadange/email-skills ~/.claude/skills/email-skills
```

**Project-level (current project only):**

```bash
git clone https://github.com/akashbhadange/email-skills .claude/skills/email-skills
```

Once placed, Claude Code discovers the skill automatically.

## Usage

Invoke the skill from any Claude Code conversation:

```
/email-skills
```

Or describe an email-related task and Claude will load it automatically based on context.

The entry point (`SKILL.md`) provides an overview and routes to the relevant sub-skill for the task at hand. Sub-skills are loaded on demand.

## Contributing

Pull requests welcome. If adding a new skill, follow the existing frontmatter format
and keep each file focused on a single domain.
