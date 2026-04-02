# Onboarding Sequences: Detailed Reference

Full sequence structures for feature discovery, feedback/NPS collection,
and milestone emails. Load this file when building or advising on these
specific automation types.

---

## Feature Discovery Sequence

**Goal:** Progressively introduce features the user has not yet tried, timed to when they
are most likely to need them. Not a feature dump; a guided path from basic to advanced use.

**Trigger:** Completion of a prior milestone. Each email unlocks when the user has finished
the previous step, not on a fixed calendar.

**When to use over onboarding drip:** Use onboarding drip to get users to first activation.
Use feature discovery after activation, to deepen engagement over weeks 2-8.

```
Email 1 (after activation):      Introduce the second most important feature.
                                   Frame it as the natural next step from what they just did.
                                   One-sentence explainer + single CTA to try it.

Email 2 (3-5 days, if Email 1 CTA not clicked):
                                   Resend with a different angle: a short use case or
                                   customer example showing why this feature matters.

Email 3 (after Email 1 CTA clicked or day 7):
                                   Third feature. More advanced. For users building
                                   a habit with the product.

Email 4 (day 14-21):              Power user feature or integration. Only send to users
                                   who engaged with Email 3.

Email 5 (day 30+):                "Here is what you have not tried yet." Personalized list
                                   of unused features relevant to their use case or role.
```

**Pitfalls to avoid:**

- Do not send feature emails to users who already use that feature. Segment by feature
  usage before sending.
- Lead with the problem the feature solves, not the feature name. "Automate your weekly
  report" beats "Introducing: Scheduled Reports."
- One feature per email. Covering three features teaches users nothing about any of them.

---

## Feedback and NPS Collection

**Goal:** Collect actionable product feedback and measure satisfaction at the right moments
in the lifecycle, not just on a quarterly schedule.

**Trigger:** Time-based milestones (day 7, 30, 90) or behavioral milestones (completed X
tasks, just resolved a support ticket).

### When to Ask for Feedback

| Timing | What to Ask |
| --- | --- |
| Day 7 (after first activation) | "What was your first impression? Any friction?" |
| Day 30 (first month complete) | NPS or CSAT: overall satisfaction |
| After support ticket resolved | "How did we do?" (CSAT) |
| After first use of a feature | "Did this work the way you expected?" |
| Day 90 (established user) | "What's missing? What would make you recommend us?" |
| Before renewal (annual plans) | "What's working well? What could improve before renewal?" |

### NPS Email Structure

```
Subject: Quick question about [Product]

Hi [first name],

How likely are you to recommend [Product] to a colleague?

[  0  1  2  3  4  5  6  7  8  9  10  ]   ← Linked scale or button row

Takes 10 seconds. Your answer shapes what we build next.

[Name]
[Product] team
```

Keep NPS emails short: one question, no padding. Include the 10-second framing.
Lengthy preamble before the question reduces response rates.

### Follow-Up by Score

NPS responses should trigger a follow-up, not go silently into a dashboard.

```
Promoters (9-10):   "Thank you. Would you be open to leaving a review or sharing
                     your story? [Link to G2 / Capterra / case study form]"

Passives (7-8):     "Thanks for the score. What would move it to a 10 for you?
                     [Open text field or reply link]"

Detractors (0-6):   Route to a human. Do not automate the response to detractors.
                     A personalized reply saves more accounts than any automated
                     win-back sequence.
```

### Open Feedback Email

For deeper qualitative feedback, use one open-ended question rather than a survey.
Long surveys are abandoned; one question gets answered.

```
Subject: What's the one thing [Product] should do better?

Hi [first name],

One question: What's the one thing [Product] should do better or that's missing for you?

Hit reply and tell us. We read every response.

[Name]
```

Reply-based feedback emails outperform link-to-survey emails on both open and response
rates. They feel personal and remove the friction of an external form.

### Feedback Collection Rules

- Do not send NPS to users active fewer than 7 days. They have not had enough time to
  form an opinion.
- Do not send feedback requests to users already in a churn prevention or re-engagement
  sequence. The timing conflicts and the ask feels tone-deaf.
- Cap feedback requests at one per 90 days per contact.
- Always acknowledge feedback. Even a brief automated "thanks, we got it" is better
  than silence.

---

## Milestone and Success Emails

**Goal:** Acknowledge meaningful moments in the user's journey. Milestone emails reinforce
product habits and create an emotional connection that drives long-term retention.

**Trigger:** User reaches a measurable threshold: number of actions completed, time as a
customer, usage volume, or a specific in-product achievement.

**Why this works:** These emails are not asking for anything. In a landscape where every
email is trying to sell or re-engage, an email that simply says "you did something
meaningful" stands out.

### Milestone Types

**Usage milestones:**
- "You just sent your 100th email."
- "You've been using [Product] for 6 months."
- "Your team has completed 500 tasks."

**Progress milestones:**
- "You've set up all 5 core features."
- "You've reached 1,000 contacts in your list."
- "You've hit a 40% open rate this month."

**Relationship milestones:**
- "You've been a customer for 1 year."
- "Welcome to [tier name] status."

### Milestone Email Structure

```
Subject: You just [milestone]. Nice work.

[First name],

[Acknowledge the milestone in one sentence. Make it specific.]

[Brief context: why this milestone matters, or what it says about their progress.]

[Optional: what's next — the natural next milestone they are working toward.]

[No hard CTA. At most: a soft invitation to share, explore, or connect.]

[Signature]
```

**Tone:** Milestone emails should feel like a tap on the shoulder, not a confetti cannon.
Genuine acknowledgment beats performative celebration. The milestone itself is the good
news; let it speak.

### Rules

- Do not attach a pitch to every milestone email. One in three can include a soft upgrade
  mention. The others should give without asking.
- Do not send milestone emails for trivial actions. "You just logged in for the 3rd time"
  is not a milestone.
- Do not make the milestone feel calculated. Commercial intent that is too obvious reads
  as manipulation, not recognition.
