# Run sheet

The organizer presents this repository for you, in 2 minutes, without having
seen it before. Write every line for them. Replace every `TODO`. Keep it to
one screen.

## Say this — 20 seconds

**Team:** Claudiu Busuioc

**Track:** personalized-growth-engines

**Who has the problem:** An outbound/growth team deciding, account by
account: is this prospect worth research and personalization effort, or
should it just get our strong generic template?

**The job this skill does:** Answers that question from the prospect's real
public website: extracts traceable evidence, derives a role-grounded buyer
hypothesis and a "why this account" angle, tests an evidence-grounded email
against a *strong* generic baseline (same offer, structure, CTA) in a blind
pass, and ends with a decision — recommended effort, winning angle, and a
reusable hypothesis. Personalized is allowed to lose; the score is a
ranking signal, not a reply prediction, and "don't personalize here" is a
valid, useful answer.

**Boundary — what it never does:** Never sends anything (draft only), and
never uses personal/individual data — only public company info and a named
role, never a named real person.

## Run this — 60 seconds

1. Codex is open at the repository root.
2. Paste [`demo/seed-prompt.md`](demo/seed-prompt.md).
3. Watch for: `demo/output/plausible.md` appearing within seconds of the
   first fetch (a `Status:` line + retrieval sources), then growing —
   evidence list, buyer hypothesis, "Why this account", both email drafts,
   the blind critique, a verdict on reply likelihood (win, loss, or tie),
   the closing **GTM signal** block ending in a recommended-effort
   decision, and the **Run ledger** appendix attesting every invariant.
   The file's LAST line is its current state; `Status: complete` there
   marks the finished artifact.
4. If the file has not appeared after 60 seconds, open the fallback — the
   one-page rendering [`demo/output/plausible.html`](demo/output/plausible.html)
   for the screen, [`demo/output/plausible.md`](demo/output/plausible.md) for
   the audit trail (both already committed).

## Show this — 25 seconds

**Result:** Open the **GTM signal** block at the end of the output: pain
hypothesis, evidence strength, personalization lift, and the decision —
personalize / go light / don't spend research time here — plus the winning
angle and a reusable hypothesis for similar accounts. Behind it sits the
full audit trail — sourced evidence, the role-grounded buyer hypothesis,
both emails, a blind score on each — so the decision is an inspectable
chain, not a self-graded claim. One run is one account's answer; the block
is shaped so runs across many accounts aggregate into message-testing data.

**Evidence:** Source URL and retrieval date on every fetched page; every
claim in the evidence list traces to a specific sourced sentence
(auditability, subscription-funded business model, self-serve pricing).
The **Run ledger** at the end attests every invariant with its evidence
inline — offer provenance, one-attempt fetches, slot arithmetic, and a
fact audit mapping every personalized claim to its evidence item — so
trust is an inspectable chain, not a promise.

**Fallback output was produced:** 2026-08-28 ~19:05–19:15, during the event
build window, by a fresh Claude Code agent running this skill cold from a
sandbox copy — only the seed prompt provided. Homepage fetched live;
comparison page from the committed snapshot, marked honestly. Full Run
ledger, style bar enforced, and one correction round recorded inside the
artifact: the agent's first style scrub attested falsely, was caught by an
external count, and the agent fixed its own emails and logged the catch.
Not fabricated, not curated by hand. A Codex re-run at the venue replaces
it if time allows.

## Evals — 10 seconds

| Case | Result | Where |
| --- | --- | --- |
| Intended | Pass — run cold by a fresh agent: personalized won on reply likelihood 4/5 vs. 3/5 against a baseline that passed the strawman bar, with the full Run ledger attested (slot arithmetic shown, hypothesis lock checked, every personalized claim mapped to evidence) | [`demo/evals.md`](demo/evals.md) |
| Insufficient evidence | Pass — real unreachable URL genuinely produced a DNS failure; skill's written behavior stops and reports rather than inventing a company | [`demo/evals.md`](demo/evals.md) |
| Failure / exclusion | Pass — skill's input contract and rules reject a named-individual request; it never touches personal data | [`demo/evals.md`](demo/evals.md) |

## Close — 5 seconds

**Reusable on:** Any public company homepage plus a named buyer-role
description — nothing here is Plausible-specific except the demo input.

**Material limitation:** The reply-likelihood score is a simulated ranking
signal, not a measured reply rate — it says which message deserves real
outreach volume, not what will happen. Persona quality depends entirely on
how much real content is on the fetched pages — a thin site produces a thin
reaction, and the evidence-strength field says so out loud. And the critique
pass isn't an independent judge (same model, same run) — it's a blind pass,
not cross-model verification.
