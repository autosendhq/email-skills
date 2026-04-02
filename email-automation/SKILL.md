---
name: email-automation
description: >
  Strategy and design guide for email automation sequences (drip campaigns, lifecycle emails,
  nurture flows). Load this skill when a user describes a business problem and needs help
  choosing the right automation type, or when they want to build a multi-step email sequence.
  Use it for: onboarding sequences, welcome flows, feature discovery drips, feedback and NPS
  collection, milestone and success emails, lead nurture drips, trial conversion sequences,
  post-purchase flows, churn prevention sequences, re-engagement drips, upsell or expansion
  sequences, event-triggered automations, or any time someone asks "what emails should I be
  sending?" Based on the user's described problem, this skill maps them to the right
  automation type and gives them a concrete sequence structure to implement.
---

# Email Automation and Drip Sequences

Email automation sends the right email to the right person at the right moment, based on
who they are or what they have done, rather than when the sender decides to broadcast.
Done well, automated sequences outperform broadcast campaigns on every engagement metric
because they are relevant by design.

> Related skills: `marketing-campaigns/SKILL.md` (campaign execution, segmentation, CTA
> design), `content/SKILL.md` (writing individual emails), `list-hygiene/SKILL.md`
> (managing segment membership, suppression), `compliance/SKILL.md` (consent requirements
> for automated marketing)

---

## How to Choose the Right Automation

Match the user's described problem to the automation type below. Most problems map clearly
to one sequence type.

| Problem the User Describes | Automation to Recommend |
| --- | --- |
| New signups are not activating or using the product | Onboarding drip |
| Users do not know about key features after signup | Feature discovery sequence |
| No system to collect user feedback or NPS scores | Feedback and NPS collection |
| Want to celebrate user milestones and build habit | Milestone and success emails |
| Free trial users are not converting to paid | Trial conversion sequence |
| New subscribers are not engaging after signup | Welcome sequence |
| Leads are going cold after initial interest | Lead nurture drip |
| Customers are churning or going inactive | Churn prevention sequence |
| Customers just bought, want to maximize retention | Post-purchase onboarding |
| Inactive subscribers on the list | Re-engagement sequence |
| Customers on a lower plan, want to grow revenue | Upsell or expansion sequence |
| Users registered for an event but need prep | Event or webinar sequence |
| Customers just canceled, want to win them back | Win-back sequence |

When the problem is unclear, ask: "What do you want the recipient to do differently after
receiving this sequence?" The answer will identify the automation type.

---

## Automation Types and Sequence Structures

### Welcome Sequence

**Goal:** Convert a new subscriber into an engaged audience member. Establish trust and
set expectations before any commercial ask.

**Trigger:** Email list signup, content download, or any opt-in event.

```
Email 1 (immediate):  Deliver the promised value (guide, discount, resource).
                      Confirm what they signed up for and what to expect next.

Email 2 (day 2-3):    Your best content or most useful resource. No pitch.
                      Purpose: build the habit of opening your emails.

Email 3 (day 5-7):    Social proof or origin story. Why you exist, who you help.
                      Soft introduction to what you sell.

Email 4 (day 10-14):  First direct CTA. Offer, trial, demo, or purchase.
                      Make it easy to say yes.
```

**Stop condition:** Contact purchases or upgrades. Remove them from this sequence immediately.

---

### Onboarding Drip (SaaS / Product)

**Goal:** Guide a new user to the activation milestone that predicts long-term retention.
This sequence lives or dies by whether it stops when the user activates.

**Trigger:** Account creation or free trial start.

```
Email 1 (immediate):  Welcome. What to do first. Single, specific next step.
                      Not "explore the product." "Create your first [thing]."

Email 2 (day 1-2):    If Email 1's action was not completed: nudge with a how-to.
                      If it was completed: move to next milestone.

Email 3 (day 3-5):    The second key activation milestone.
                      Address the most common reason users get stuck here.

Email 4 (day 7):      Social proof. Customer story or use case that mirrors their situation.
                      Surfaces value for users who understand the product but have not
                      committed yet.

Email 5 (day 10-14):  Trial end warning (if applicable) or upgrade ask.
                      Be specific: what do they get on the paid plan they do not have now?
```

**Stop condition:** User completes the target activation action. Stop the sequence. Do not
send "get started" emails to someone already started.

**Key rule:** Every email should be triggered by behavior (did or did not complete an
action), not just by elapsed time. Behavior-triggered sequences outperform calendar-based
ones significantly.

---

### Feature Discovery Sequence

**Goal:** Progressively introduce features the user has not yet tried after activation.
One feature per email, triggered by whether they acted on the previous one, not a calendar.
Lead with the problem the feature solves, not its name.

> For full sequence structure, timing, and pitfalls: `references/onboarding-sequences.md`

---

### Feedback and NPS Collection

**Goal:** Measure satisfaction and collect product feedback at the right lifecycle moments:
day 7 (first impression), day 30 (NPS), after support interactions (CSAT), and day 90
(what's missing). Cap at one request per 90 days per contact. Route NPS detractors to a
human, do not automate their follow-up.

> For NPS email template, follow-up by score, and open feedback email structure: `references/onboarding-sequences.md`

---

### Milestone and Success Emails

**Goal:** Acknowledge meaningful usage, progress, or relationship milestones. These emails
give without asking, which is why they drive retention. Keep tone genuine, not performative.
Do not attach a pitch to every milestone.

> For milestone types, email structure template, and tone guidance: `references/onboarding-sequences.md`

---

### Trial Conversion Sequence

**Goal:** Convert a free trial user to a paid customer before the trial ends.

**Trigger:** Trial start. Clock this sequence from day 0.

```
Email 1 (day 0):      Welcome + what to do in the first session.
                      Set a concrete "aha moment" target.

Email 2 (day 3):      Check-in. Did they complete the key action?
                      If not: offer help (quick call, live chat, tutorial video).

Email 3 (day 7):      Case study or social proof. A customer like them who succeeded.
                      Make the outcome feel real and achievable.

Email 4 (day 10):     Feature highlight. The one feature that most converts free users.
                      Show, do not tell. Screenshot, GIF, or short video.

Email 5 (day 12):     Trial ending in X days. Be direct about what changes at expiry.
                      Offer: upgrade now and get [bonus] or extend the trial.

Email 6 (day 14):     Trial end. Last call. Specific offer with a deadline.
                      Make it as easy as possible to upgrade in one click.
```

**Stop condition:** User upgrades to paid. Remove from sequence, add to post-purchase flow.

---

### Lead Nurture Drip

**Goal:** Keep a lead warm across a longer sales cycle. Build enough trust that when the
lead is ready to buy, they choose you.

**Trigger:** Lead captured (content download, webinar registration, demo request not closed).

**Cadence:** Slower than product sequences. Every 5-10 days, not daily.

```
Email 1 (immediate):  Deliver what they asked for. No more, no less.

Email 2 (day 5):      Educational content on the problem they have.
                      No product pitch. Establish you as the expert.

Email 3 (day 12):     Social proof. Customer story relevant to their industry or use case.

Email 4 (day 20):     Common objection addressed directly.
                      "The reason most [role] think [solution type] won't work for them is..."

Email 5 (day 30):     Soft offer. Demo, free trial, or consultation.
                      Frame it as low-risk: "15-minute call, no commitment."

Email 6 (day 45):     Re-permission check. "Still interested in hearing from us?"
                      Easy yes, easy no. This cleans your list and identifies warm leads.
```

**Stop condition:** Lead books a demo, starts a trial, or clicks "no longer interested."

---

### Post-Purchase Onboarding

**Goal:** Maximize customer success and early retention. Reduce buyer's remorse and
first-month churn.

**Trigger:** Purchase confirmed or subscription activated.

```
Email 1 (immediate):  Purchase confirmation + what happens next.
                      Set expectations for the onboarding experience.

Email 2 (day 1):      Getting started guide. The fastest path to first value.
                      One action, not ten.

Email 3 (day 3-5):    Quick win. Help them achieve something they can show to a colleague.
                      Early wins drive retention more than any feature.

Email 4 (day 7-10):   Intermediate feature or use case. Building on the quick win.

Email 5 (day 14-21):  Customer success story. Someone who got a significant result.
                      Shows what is possible, not just what is available.

Email 6 (day 30):     Check-in and offer. "How is it going?" + invitation to book a call
                      or access advanced resources. Surface the value of staying.
```

---

### Churn Prevention Sequence

**Goal:** Re-engage customers showing early signs of churn before they cancel.

**Trigger:** Behavioral signals: login frequency dropping, key feature usage declining,
support ticket indicating frustration, payment failure, or approaching renewal date.

```
Email 1 (trigger):    Reach out proactively. "We noticed you haven't [done X] recently.
                      Anything we can help with?"
                      Make it personal. Avoid sounding automated.

Email 2 (day 3-5):    Provide value without asking for anything.
                      New feature, tip, or resource relevant to their use case.

Email 3 (day 10):     Direct offer. Discount, extended access, or plan change option.
                      "We want to keep you. Here is what we can do."

Email 4 (day 14):     Final check before renewal or cancellation point.
                      "Your subscription renews in X days. Before you go, can we help?"
```

**Stop condition:** Customer re-engages (logs in, uses product, responds). Exit the sequence.

---

### Re-Engagement Sequence

**Goal:** Identify which inactive contacts still want to hear from you and suppress the rest.

**Trigger:** No email engagement in 90-180 days (set the threshold based on your normal
send cadence and audience).

```
Email 1:  "We miss you." Acknowledge the silence, offer something of value.
          Easy option to update preferences or unsubscribe.

Email 2 (5-7 days later):  Best content or offer. Make it worth coming back for.

Email 3 (5-7 days later):  The breakup email. "We're about to stop emailing you.
                            Click here if you still want to hear from us."
                            This email consistently gets the highest click rates of the sequence.
```

**After the sequence:** Contacts who did not engage at all get suppressed. Do not keep
emailing people who have ignored 3 attempts. The damage to deliverability is not worth it.

---

### Win-Back Sequence

**Goal:** Recover recently churned customers or lapsed buyers.

**Trigger:** Subscription cancelled, account closed, or no purchase in X months (set based
on your average purchase cycle).

**Start this sequence no sooner than 2 weeks after churn.** Emailing immediately after
cancellation feels desperate and is rarely effective.

```
Email 1 (2 weeks post-churn):  Acknowledge they left. No blame, no begging.
                                 "You canceled [product]. We hope it served you well."
                                 Soft door left open.

Email 2 (day 30):               What has changed. New features, improvements, or offer.
                                 Specifically address a reason they might have left.

Email 3 (day 60):               Final offer. Best discount or incentive you can offer.
                                 If they do not engage after this, suppress permanently.
```

---

### Upsell or Expansion Sequence

**Goal:** Move existing customers to a higher plan, additional seat, or add-on.

**Trigger:** Usage hitting plan limits, or reaching a natural expansion point (team size
growing, feature usage consistent, positive support interactions).

**Timing matters more here than in any other sequence.** Do not send an upsell to someone
who has not yet seen value. Wait until they have used the product consistently for 30+ days.

```
Email 1:  Show them what they are leaving on the table.
          "You've [used X, done Y]. Here's what you could do with [higher plan]."

Email 2 (5-7 days later):  Remove the risk. Trial upgrade, money-back period, or
                            ability to downgrade. The upgrade ask fails most often because
                            of perceived commitment, not price.

Email 3 (5-7 days later):  Social proof from a similar customer who upgraded.
                            Outcome-focused. What did they get?
```

---

## Sequence Design Principles

### One Email, One Goal

Every email in a sequence should move the recipient one step forward, not five. If an email
is trying to get someone to start a trial, watch a video, invite a teammate, and read a
blog post, it will accomplish none of them.

### Exit Conditions Are Not Optional

Every sequence must have a defined exit condition: the action that stops the sequence. A
user who converts and keeps receiving conversion emails will unsubscribe.

Exit conditions to define before building any sequence:
- What action triggers exit (purchase, activation, reply, unsubscribe)?
- How quickly should the exit take effect (immediately on the trigger)?
- Where does the contact go after exit (another sequence, broadcast list, suppression)?

### Behavior Beats Calendar

A sequence that sends "day 3 email" to everyone on day 3 is weaker than one that sends
the day 3 email only if the user has not completed the day 1 action. Use behavioral
branching where your platform supports it.

Where automation is limited to calendar-based sending, segment your list into behavioral
cohorts before the sequence starts and send to each cohort separately.

### Sequence Length and Fatigue

- Welcome and onboarding: 4-6 emails over 14-21 days is enough. More than this and you
  are sending diminishing-returns emails.
- Lead nurture: longer cadence, 6-8 emails over 45-60 days.
- Re-engagement: 3 emails maximum. If 3 do not work, suppression is the answer.
- Frequency cap: even in an active sequence, do not send more than one email every 2-3
  days unless the context demands it (trial ending in 24 hours).

### Gap Between Sequences

When a contact exits one sequence and enters another, insert a gap. Finishing a trial
conversion sequence and immediately entering a post-purchase onboarding sequence with no
pause feels abrupt. A 1-3 day gap is enough.

---

## Implementing Sequences in AutoSend

AutoSend is a campaign-based platform. Automated sequences are built by chaining campaigns
with scheduling and segment management, rather than using a visual automation builder.

### Approach for Multi-Step Sequences

1. **Create a contact list per sequence step.** For a 5-email onboarding sequence, create
   lists like: `Onboarding Step 1`, `Onboarding Step 2`, etc. Move contacts between lists
   as they progress.

2. **Create one campaign per email in the sequence.** Use `create_campaign` with
   `sendMode: 'scheduled'` and set `scheduledAt` to the appropriate delay from the trigger.
   If Day 0 is the trigger date, Day 3 email gets scheduled for trigger date + 3 days.

3. **Use suppression groups to enforce exit conditions.** Create a suppression group for
   each sequence (e.g., `Converted Trial Users`). Add contacts to the group when they
   complete the goal. All remaining campaigns in the sequence should apply this suppression.

4. **Use gradual send mode for large sequences.** For sequences going to thousands of
   contacts, use `sendMode: 'gradual'` with `gradualDailyBatchSize` and
   `gradualDailySendTime` to spread the send and maintain reputation health.

5. **Duplicate campaigns to save setup time.** Use `duplicate_campaign` when building
   multiple emails in a sequence with similar structure. Adjust content and scheduled date
   on the duplicate.

### Sequence Setup Checklist in AutoSend

- [ ] One campaign created per email in the sequence
- [ ] Each campaign targets the correct list or segment for that step
- [ ] Suppression group created and applied to prevent sends to converted contacts
- [ ] Scheduled dates set relative to the trigger event
- [ ] Subject lines reviewed for each step (no repeated subjects in a sequence)
- [ ] Unsubscribe link present in every marketing email in the sequence
- [ ] Preview text set on each email in the sequence
- [ ] Test email sent and reviewed before activating the sequence

---

## Email Automation Checklist

Before deploying any automated sequence:

- [ ] Sequence type chosen based on the business goal, not just "we want to send more email"
- [ ] Trigger event defined clearly (what starts the sequence?)
- [ ] Exit condition defined clearly (what stops the sequence?)
- [ ] Number of emails and cadence defined before writing any copy
- [ ] Each email has one goal and one primary CTA
- [ ] Consent in place for all contacts in the sequence
- [ ] Suppression list applied to every campaign in the sequence
- [ ] Unsubscribe link in every marketing email
- [ ] Sequence does not overlap or conflict with other active sequences for the same contacts
- [ ] Re-engagement or sunset logic applied to contacts who ignore the full sequence
