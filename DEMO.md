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
public website: extracts traceable evidence, locks what the seller may
claim, rates **offer fit** — whether the evidence gives a credible reason
to pitch THIS offer at all — and ends in TWO decisions: account action
(pursue / probe first / deprioritize) and messaging effort (generic /
light / personalize). On a grounded bridge it blind-tests an
evidence-grounded email against a *strong* generic baseline; on a low-fit
account it can WITHHOLD the personalized test rather than manufacture one.
Personalized is allowed to lose; the score is a ranking signal, not a
reply prediction; knowing a lot about a company is not a reason to pitch
it.

**Boundary — what it never does:** Never sends anything (draft only), and
never uses personal/individual data — only public company info and a named
role, never a named real person.

## Run this — 60 seconds

1. Codex is open at the repository root.
2. Paste [`demo/seed-prompt.md`](demo/seed-prompt.md).
3. Watch for: `demo/output/plausible.md` appearing within seconds of the
   first fetch (a `Status:` line + retrieval sources), then growing —
   evidence list, offer-fit rating, buyer hypothesis, the offer-account
   bridge, both email drafts, the blind critique, a verdict on reply
   likelihood (win, loss, or tie), the closing **GTM signal** block ending
   in a recommended-effort decision, and the **Run ledger** appendix
   attesting every invariant. The file's LAST line is its current state;
   `Status: complete` there marks the finished artifact.
4. If the file has not appeared after 60 seconds, open the fallback — the
   one-page rendering [`demo/output/plausible.html`](demo/output/plausible.html)
   for the screen, [`demo/output/plausible.md`](demo/output/plausible.md) for
   the audit trail (both already committed).

## Show this — 25 seconds

**Result:** Open the **GTM signal** block at the end of the output: pain
hypothesis, evidence strength, **offer fit**, the personalization result
(wins / ties / loses / withheld), and the two decisions — **account
action** and **messaging effort** — with the why, a next probe, and a
reusable hypothesis. "Interesting company, weak reason to pitch this
offer" is a first-class outcome, not a failure. The one-line stage claim:
without the skill, the agent optimizes for producing an answer; with it,
the agent can conclude the account isn't justified — the no-skill baseline
in `demo/evals.md` fabricated reply rates, ARR and a priority score from
the same input. Behind the block sits the full audit trail — sourced
evidence, locked seller claims, per-sentence provenance, blind scores — so
the decision is an inspectable chain, not a self-graded claim.

**Evidence:** Source URL and retrieval date on every fetched page; every
claim in the evidence list traces to a specific sourced sentence
(auditability, subscription-funded business model, self-serve pricing).
The **Run ledger** at the end attests every invariant with its evidence
inline — offer provenance, one-attempt fetches, slot arithmetic, and a
fact audit mapping every personalized claim to its evidence item — so
trust is an inspectable chain, not a promise.

**Fallback output was produced:** 2026-08-28 ~19:45–20:00, during the event
build window, by a fresh Claude Code agent running the redesigned skill
cold from a sandbox copy — only the seed prompt provided, both pages
fetched live, full Run ledger with per-sentence provenance, one correction
round recorded inside the artifact (a falsely-attested counted zero caught
externally; one inference retagged from evidence to hypothesis after a
live re-check). A parallel no-skill baseline on the same input fabricated
reply rates, ARR and priority scores — the contrast is recorded in
`demo/evals.md`. Not fabricated, not curated by hand. A Codex re-run at
the venue replaces it if time allows.

## Evals — 10 seconds

| Case | Result | Where |
| --- | --- | --- |
| Intended | Pass — run cold by a fresh agent under the redesigned skill: personalized WON the blind test 4 vs 3, and the skill still recommended staying generic — offer fit low, bridge "No strong bridge found", the win named recognition-driven; full Run ledger with per-sentence provenance | [`demo/evals.md`](demo/evals.md) |
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
