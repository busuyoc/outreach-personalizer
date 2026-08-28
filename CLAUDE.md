# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A submission repo for the GTM Skillathon (28 Aug 2026, Formidable Builders). It is not a
general software project — it is a single agent skill plus the demo evidence needed to
have it judged by an organizer running Codex from a fresh clone, with no API keys and no
installs. `AGENTS.md` is the canonical build contract; `RULES.md` is the canonical event
rules and judging criteria. Read both before making changes — this file only orients you
faster, it doesn't override them.

## The entry skill (current, on disk)

`.agents/skills/outreach-personalizer/SKILL.md` — the only skill declared in
`submission.json` as `entry_skill`. It is **Markdown-only, no code**: given a company URL
and a buyer role, it fetches the company's real homepage (falling back to a committed
snapshot in `demo/input/` if the fetch fails), simulates that buyer role's reaction, writes
a strong generic email and an evidence-grounded one (same offer, structure, CTA), blind-
scores both, and ends `demo/output/<company-slug>.md` with a **GTM signal** block (pain
hypothesis, evidence strength, winning angle, reusable signal) plus an optional HTML
rendering. Reference data for personas lives in
`.agents/skills/outreach-personalizer/references/personas.md` (never read at run time).

The project previously prototyped a Bun/TypeScript pipeline (`src/scrape.ts` → Apify,
`src/persona.ts`/`src/draft.ts`/`src/eval.ts` → OpenAI) under a `gtm-outreach-personalizer`
skill folder, but pivoted to the Markdown-only `outreach-personalizer` skill above so the
judged run needs no API keys and no installs. That pipeline's code and skill folder have
been removed; don't resurrect them unless the user explicitly asks to go back to that
approach.

## Organizer-provided skills (do not touch, never list in submission.json)

- `.agents/skills/skillathon-guide/SKILL.md` — explains the event/rules, helps scope a job.
- `.agents/skills/skillathon-submit/SKILL.md` — validates, commits, pushes, and files the
  submission issue. Runs `scripts/validate.mjs` (structure/clean checks) then
  `scripts/submit.mjs` (pushes and files the GitHub issue).

## Commands

```bash
# Validate the submission structure without filing anything (safe, read-only against the index)
git add -A && node .agents/skills/skillathon-submit/scripts/validate.mjs

# Check submission readiness without filing
node .agents/skills/skillathon-submit/scripts/submit.mjs --check

# File the actual submission (commits, pushes, opens a GitHub issue) — only when the user asks
node .agents/skills/skillathon-submit/scripts/submit.mjs
```

`validate.mjs` reads the **git index**, not the working tree — always `git add` before
running it or it will check stale staged content. There is no build/lint/test command
beyond this validator; the skill itself is Markdown, not compiled code.

## Required structure (enforced by validate.mjs)

Everything `submission.json` points to must exist, be committed, and be non-empty:

| Field | Current value |
| --- | --- |
| `entry_skill` | `.agents/skills/outreach-personalizer/SKILL.md` |
| `seed_prompt` | `demo/seed-prompt.md` — must literally invoke `$outreach-personalizer` and name the input path |
| `input` | `demo/input/plausible.md` |
| `output` | `demo/output/plausible.md` (the fallback shown if the live run stalls) |
| `evals` | `demo/evals.md` — needs "Intended", "Insufficient evidence", "Failure" cases each with a pass/fail cell |
| `run_sheet` | `DEMO.md` — the organizer's word-for-word 2-minute script |

Any skill folder under `.agents/skills/` not listed in `submission.json`'s `skills` array
triggers a `skill-undeclared` warning (not a hard error).

## Working on this repo

- Changes to the skill's behavior go in `outreach-personalizer/SKILL.md` — it's an
  instruction set for an agent, not code to compile. Keep steps imperative, keep the file
  under ~80 lines (see `.agents/skills/skillathon-guide/references/skill-template.md`).
- Never invent eval results, fallback output, sources, or retrieval dates — `demo/evals.md`
  and `demo/output/plausible.md` must reflect a genuine run, and the file headers say when
  and how they were produced.
- The skill must never send anything or touch real individuals' personal data — it stops at
  a reviewable draft, and only operates on a company + a named role, never a named person.
- Before committing, run `git status` and confirm nothing stray is staged — the index has
  been reconciled once already (stale TS-pipeline files and the unused skill folder removed).

## Pre-build review — 28 Aug 2026, ~17:45 (insights, keep)

State verified before the build window: `validate.mjs` green, 0 errors / 0 warnings. Live-site
check: every claim in both `demo/input/plausible.md` snapshots was confirmed present on the
live homepage and `/vs-google-analytics`, and the homepage genuinely links the comparison
page — the skill's step-2 path exists in reality, not just in the demo script.

Four mechanism holes were found and closed in `SKILL.md` (all inside the decided scope —
Markdown-only, same job, same demo):

1. **All-or-nothing output vs the 60-second stage.** The organizer switches to fallback at
   ~60s; the file used to be written only at the end, so a working-but-slow run showed
   nothing. Now step 7 writes incrementally (evidence + persona first) — a cut-off run
   leaves a truthful partial artifact on screen.
2. **Undefined offer on a fresh input.** Both emails share "the same offer", but only the
   demo input happened to carry one; on any new company file the comparison was undefined —
   a reusability hole. Input now defines the offer source and the derive-if-absent rule.
3. **Clock-keyed instructions.** "~60s total, skip step 3 if past it" asks the agent to do
   something it cannot (sense elapsed time). Step 3 now keys on an observable — fewer than
   two usable evidence items — and steps 1–2 are one-attempt-no-retry by letter.
4. **Blind-critique freedoms.** "(order varied between runs)" was unimplementable in a
   single run, and scores had no audit hook. Slots are now assigned mechanically
   (slug-length parity) and every score must quote the sentence that justifies it. True
   cross-model blindness stays out of scope and stays honestly declared in `DEMO.md`.

Deliberate choices, kept: `references/personas.md` is never read at run time (now stated in
the file — it documents target scoping and the checkable failure mode); `SKILL.md` runs
~15 lines over the template's ~80-line guide because the anti-strawman rationale *is* the
differentiator, not padding. Consistency fixes: personas' stale step pointer (2→4), DEMO's
verdict promise now covers win/loss/tie (the Runs gate checks the run "produces what
DEMO.md promises"), fallback/output provenance notes carry the step-evolution honestly.

Still open for the venue (in order): first commit + push + submit EARLY once the window
opens (`submit.mjs --check` currently STOPs on the uncommitted tree; AGENTS.md says accepted
submission by 19:45, improve after); re-run all three evals in Codex per the checklist in
`demo/evals.md` (intended case is REQUIRED — the recorded run predates the tightening);
update the output footer after the live re-run; resubmit.

## Round 2 — external critique triaged against RULES.md (~18:00, insights, keep)

A long external (GPT) methodology critique was judged against what the event actually scores,
not adopted wholesale. **Adopted** — the three HIGHs: symmetric email targets (generic written
FIRST as the strongest role-level baseline; personalized may only improve it with cited
evidence — no new capabilities, offer components, or CTA changes), the invariant four-part
structure (hook → problem hypothesis → offer/value → low-commitment CTA) so "same structure"
is operational, and role-only offer derivation BEFORE any fetching (else the offer itself is
quietly personalized). Also adopted as cheap honesty/reproducibility: "blind critic pass" not
"independent" (same model — say so), rule kept out of the critic's context, quote-per-score
named an audit hook not proof, no latent company knowledge (famous companies earn evidence
like unknown ones), evidence count never forced ("fewer, honestly" beats manufactured items),
deterministic second-page choice (highest-priority category, first link in document order,
observable qualifying criteria), per-source live/cached marks, and a `Status: in progress /
complete` marker so a cut-off incremental artifact can't be mistaken for a finished one.

**Adopted as the product point (owner directive — results, not process):** the output now ends
in a **GTM signal** block — buyer pain hypothesis, evidence strength (low strength = "this
account doesn't justify custom outreach yet", itself actionable), personalization lift,
winning angle, and a reusable signal written to aggregate across runs. Optional self-contained
HTML rendering AFTER the markdown completes (never blocking — the 75s Runs gate outranks
prettiness). A pre-event fallback rendering exists at `demo/output/plausible.html` — strictly a
reformatting of the genuine markdown fallback, with no GTM signal block (that block postdates
the run; inventing one would fabricate results — hard rule 3).

**Rejected, with reasons:** in-skill aggregation/swarm mechanics (RULES score ONE narrow job;
aggregation is presentation framing, one line in DEMO); randomization of critic slots (a demo
isn't a population experiment — deterministic + hidden from the generator is the defensible
claim, and it's worded exactly that way); showing illustrative aggregate numbers in committed
artifacts (honesty gate — illustrative data stays off the record); any further mechanism
(GPT's own closing warning: past this point complexity is a bigger timeout risk than the bias
it removes). Conceptual language shifted everywhere from "tests whether personalization works"
to "tests whether an evidence-grounded workflow beats a strong generic baseline on a simulated
reply-likelihood signal — a ranking signal, not a reply prediction."

## Round 3 — decision layer + fresh-agent litmus (~18:15–18:45, insights, keep)

Third external critique triaged the same way. **Adopted:** framing locked to ONE problem
hypothesis across both emails; blindness stated precisely (blind to generation intent, NOT to
treatment identity — the critic can infer which email is personalized from content); "Reusable
signal" → "Reusable **hypothesis**" (one account supports a hypothesis to test, never a
cross-account pattern claim); evidence-strength rubric (high = 2+ specific traceable claims on
a buyer-relevant angle / medium = 1 strong or several weak / low = generic positioning only);
**Recommended effort** decision derived from evidence × lift (personalize / go light / don't
spend research time — the "don't" answer is itself the product value); **Why this account**
opportunity hypothesis; positioning-signal hint when the second page is a comparison page;
persona shrunk to bullets; step-3 search retargeted from social chatter to commercial signals;
output file created immediately after step 1; HTML gated on EXPLICIT request (mechanically
decidable — GPT's "never during a live run" would require the agent to self-classify the run).
**Rejected:** Apify anywhere near the judged path (no credentials on the jury laptop — dev-lab
/ future-scale only), person-level enrichment (different product, personal-data hard rule),
competitor-analysis expansion (scope).

**Litmus — the skill run cold by the smallest agent** (Haiku, sandbox copy, only the runtime
convention + verbatim seed prompt): completed end-to-end in ~86s with live fetches, correct
second-page choice (comparison), step 3 correctly skipped, offer taken from the input, zero
invented facts, zero latent-knowledge leakage, all six signal fields, hypothesis-phrased
reusable line, and NO html (the explicit-request gate held). Three rule violations, each a spec
weakness fixed on the spot: (1) it swapped the problem hypothesis in the personalized email and
the verdict *celebrated* the drift → the hypothesis is now a WRITTEN artifact both emails must
address, checked before scoring, drift = invalid comparison; (2) it ignored slug parity
(plausible = 9 = odd → personalized should be A; it put personalized in B) → the arithmetic
must now be SHOWN in the output ("an assignment that isn't shown wasn't computed"); (3) it
appended status lines instead of updating the first line, leaving a stale "in progress" at the
top → spec now matches the natural mechanic: every write appends a status line, the LAST line
of the file is the truth. Lesson worth keeping: a rule that produces no visible artifact does
not reliably survive contact with a small agent — make every invariant something the output
must SHOW. Timing note: 86s on Haiku ≈ the 75s gate is tight; the early-visible file plus
genuine fallback is the designed mitigation (RULES: timeout with genuine fallback = pass with
warning).
