# Outreach comparison — Plausible Analytics

## Retrieval

- https://plausible.io — retrieved 2026-08-28 (live fetch)
- https://plausible.io/vs-google-analytics — retrieved 2026-08-28 (live fetch,
  step 2: one linked comparison page)

See `demo/input/plausible.md` for the cached snapshots used as fallback.

**Buyer role simulated:** Head of Growth

## Evidence list

1. "GA4's source is not auditable; Plausible's is open source on GitHub." — `/vs-google-analytics`
2. "Plausible collects no personal data and requires no consent banner." — `/vs-google-analytics`
3. "Self-serve pricing, $9–19/mo, funded by subscriptions — not by monetizing data." — homepage pricing + `/vs-google-analytics` business-model claim

## Persona reaction

**Role prior** (generic to this buyer type, not company-specific): a Head of
Growth at a small, lean team is generally skeptical of vague "AI-powered
growth" pitches without proof, and doesn't want tooling overhead.

**Company evidence:** items 1–3 above.

**Inference:** Plausible has publicly staked a position against opaque,
data-hungry tooling and against ad/data-monetized business models — so the
generic role-typical skepticism toward an unproven "AI agent" pitch is
sharper here than average, not just generic vendor fatigue.

**Resonates:** transparency/auditability framing, subscription-funded (not
ad-funded) business model, no dark patterns.

**Objections:** an opaque "AI agents automate your GTM" pitch has the same
shape as the black-box tooling they argue against; no sign of research; this
is a small self-serve team, not an enterprise GTM org with a function to
automate.

## Emails

**Email A:**
> Hi — most growth teams say a good chunk of outbound gets ignored because
> it's clearly not researched. We built a tool that pulls public info on
> each prospect and drafts a genuinely specific opening line automatically,
> instead of reps starting from a blank template. Worth 10 minutes to see if
> it'd save your team time on outreach?

**Email B:**
> Hi — saw your GA4 comparison leads with "source not auditable" vs. yours
> being open source. Same standard I'd hold outreach tooling to: this only
> uses public info, no tracking, nothing you can't verify. You're self-serve
> and funded by subscriptions, not data — which is honestly why most
> outbound-automation pitches probably don't fit a team like yours. If
> there's one manual prospect-research step still eating time, I'd rather
> hear about that than pitch a call.

## Independent critic pass (blind — evidence list + emails only, no
objections list, no generation rationale)

**Email A:** genuinely competent cold email — real problem hypothesis
(unresearched outbound gets ignored), role-relevant language, low-commitment
CTA. Nothing about it signals any knowledge of *this* company; could go to
any growth team. Reply likelihood: **3/5**. Specificity: **1/5**.
Credibility: **4/5** (doesn't overclaim, nothing to stretch).

**Email B:** clearly did real homework — references a specific real
argument (auditability) instead of a generic privacy nod, and the
self-effacing "probably don't fit a team like yours" reads as honest rather
than salesy, which lowers reply friction. The ask itself is soft ("if
there's one manual step") which could also go unanswered from inertia.
Reply likelihood: **4/5**. Specificity: **5/5**. Credibility: **5/5** (the
subscription/self-serve claim is accurate and used correctly, not
stretched).

## Reveal

Email A = generic. Email B = personalized.

## Verdict

Personalized wins on reply likelihood (4/5 vs. 3/5) against a genuinely
strong generic competitor — not a strawman. The margin is real but not a
landslide, which is the more credible result: specificity (5 vs. 1) and
credibility (5 vs. 4) both point the same direction as reply likelihood, so
no tie-break was needed this run. The win traces to one specific, honestly-
used piece of evidence (auditability positioning), not to volume of
personalization or to the generic email being deliberately weak.

---
*Produced 2026-08-28, ahead of the event, via Claude Code, homepage +
comparison-page fetch and the blind critique genuinely run against the live
plausible.io site. Rewritten same day after an external review (via ChatGPT)
flagged that the original generic baseline was a strawman and the scoring
was structurally biased toward personalization winning — this version uses
a competent generic competitor and lets personalized lose if the evidence
doesn't support it. The skill's steps were tightened again later the same
day (see `demo/evals.md` run context); this file reflects the steps as they
stood at this run. Re-run live at the venue through Codex before final
submission — this file is the fallback if that live run stalls or fails.*
