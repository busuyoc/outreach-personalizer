---
name: outreach-personalizer
description: Given a company URL and a buyer role, fetches the company's real public site, rates whether the evidence gives a credible reason to pitch this offer at all (offer fit), blind-tests an evidence-grounded email against a strong generic baseline on simulated reply likelihood, and distills an actionable GTM decision — evidence strength, offer fit, recommended effort, winning angle. Use when the user names a company or URL and a buyer role and asks for personalized outreach, a pitch, message testing, account fit, or GTM signal on a prospect.
---

# Outreach personalizer

What this skill actually tests: ACCOUNT-MESSAGE FIT — whether this company's
public evidence gives a credible reason to pitch THIS offer, and whether
grounding the message in that evidence beats a *strong* generic baseline on
a simulated reply-likelihood signal. Not a strawman contest, and not a
general claim that "personalization works." Knowing a lot about a company is
not a reason to pitch it: rich evidence with no bridge to the offer means
the honest output is "stay generic" — and that answer is product, not
failure. The score is a cheap, inspectable ranking signal, not a prediction
of real reply rates. The deliverable is the GTM signal block at the end
(pain hypothesis, evidence strength, OFFER FIT, recommended effort, winning
angle, reusable hypothesis), with the emails and the blind scores as its
audit trail. If the generic email is weak, the result proves nothing.

## Input

A company URL and a buyer role (e.g. "Head of Growth"), given in the prompt or
named by a file under `demo/input/`. If either is missing, ask for both and stop.
The buyer role is a person AT that company — the recipient of the outreach. You
write as a third-party sender pitching the offer TO them: the company is the
prospect, never the sender, and never a tool the buyer "chose" or "switched
to" — inventing a purchase history for the buyer is fabrication.
Optionally, a one-line offer to pitch (the demo input's "Generic pitch to react
to" line is this). If no offer is given, derive one from the buyer role ALONE —
before fetching or reading any company evidence, so the offer itself is not
quietly personalized — state it explicitly in the output, and use that same
offer in both emails.
**Offer claim lock** — written down BEFORE fetching any company evidence:
exactly what the seller is allowed to claim. Only capabilities, outcomes,
proof points, integrations, or positioning contained in the given or
explicitly derived locked offer, plus any seller capabilities or proof
points the user supplied.
Do not infer seller capabilities from the category: if the input says only
"We help teams automate their GTM workflows with AI agents", that sentence
is the ENTIRE allowed claim set — "account research", "outreach sequencing",
"qualification", "reduces headcount", "compliance-safe", "designed for
bootstrapped teams" and similar are all unsupported unless the user supplied
them. The input may optionally list 1–3 seller capabilities or proof points;
those join the lock. Both emails use the same locked claims and nothing
beyond them.

## Steps

1. Fetch the company's homepage — one attempt, never retry. Record the exact
   URL and today's date as the retrieval date. If the fetch fails or
   stalls, fall back to the cached snapshot in `demo/input/` for this company
   if one exists, and say so explicitly — never describe cached content as
   live. If there is no live fetch and no cached snapshot, report
   "insufficient evidence: could not reach `<url>`" and stop.
2. One homepage is thin. Fetch exactly one more page, directly linked from
   the homepage: take the highest-priority category present — comparison,
   then pricing, then product/use-case, then about — and within that
   category the first directly linked URL in document order (one attempt,
   never retry). Fetch it only if it plausibly adds at least
   one of: pricing, comparison language, a quantified outcome, or a
   concrete positioning statement not already on the homepage. Record its
   URL and retrieval date. If nothing qualifies or the fetch fails,
   continue with the homepage only — additive, never blocking.
3. Only if the evidence list would still have fewer than two usable items
   after steps 1–2: run ONE public search for a commercial signal — pricing,
   comparison, launch, or customer-story material (e.g. `<company> pricing`)
   — not social chatter. If blocked, slow, or empty, drop it silently and
   continue immediately — one attempt, no retry. Never fetch or cite
   anything naming a private individual. With two or more usable items
   already in hand, skip this step entirely — in a timed demo the search is
   the first thing to cut.
4. Build an **Evidence List** of at most 3 items (honestly fewer when the
   pages support fewer — never lower the bar to hit a count). Each item
   has EXACTLY two fields, and nothing else:
   - **Observed:** one concrete statement directly supported by the
     retrieved page. No "suggests", "signals", "implies", "likely", no
     buyer interpretation, no extrapolation. Boring enough that a reviewer
     can verify it without agreeing with any reasoning.
   - **Source:** the exact URL or cached-snapshot reference.
   Interpretation lives ONLY in the Buyer Hypothesis below. For
   company-specific claims use ONLY this list — ignore anything already
   known about this company from training data; a famous company earns its
   evidence the same way as an unknown one. If the second page was a
   comparison page, one Observed should capture the dimension the company
   differentiates on.
   Then rate **Offer fit**, independently from evidence strength:
   high = retrieved evidence directly exposes a problem, goal, or
   constraint the LOCKED offer addresses · medium = ONE credible
   evidence-backed inference connects the company situation to the locked
   offer · low = the company is well understood, but no retrieved evidence
   establishes a meaningful reason this offer should matter to it. Offer
   fit answers "why pitch THIS offer to THIS account?", not "can I
   personalize an email?". If offer fit is LOW, do not turn unrelated
   company facts into a problem merely to create a personalized angle.
   Then build a role-grounded buyer hypothesis (a hypothesis about a role,
   not a simulation of a real person, and NOT a biography — never invent
   lived experience: no "this pain is lived", no imagined incidents the
   buyer "has been through") for the offer being pitched (given or
   derived — see Input), in compact bullets, keeping three things distinct:
   - **Role prior** (1–2 bullets) — what this kind of buyer typically cares
     about, independent of this company; never counts as company evidence.
   - **Evidence-backed concerns** (1–3 bullets) — where a cited evidence
     item makes a role-typical concern especially likely here.
   - **Missing** (1–2 bullets) — what you'd want to know but don't.
   An inference may inform the hypothesis, but must never be phrased as a
   known company fact unless the evidence explicitly states it. Close the
   section with the **Offer-account bridge** — exactly ONE sentence, hard
   limit: what evidence connects this account to THIS offer ("their
   positioning suggests X may matter", never "they need X") — or exactly
   the sentence "No strong bridge found." A real operator needs the system
   able to say: interesting company, weak reason to pitch this. Do not
   invent facts not present in the sources.
5. First write down THE problem hypothesis: one role-level sentence that
   BOTH emails must address, printed in the output above the emails. It
   must be derivable from the buyer role + locked offer ALONE, and must
   remain valid if the company evidence were hidden — evidence may change
   only the personalized framing, never the underlying problem; a
   hypothesis that quietly encodes company facts contaminates the generic
   baseline.
   **Branch on offer fit.** If offer fit is LOW: still write the strong
   generic baseline, but do not force a prospect-specific problem claim —
   the personalized version may reference one factual company Observed
   ONLY if it can do so without asserting an unsupported need. If no
   responsible bridge exists, write exactly "Personalized test withheld:
   no grounded bridge to the offer.", skip the rest of this step's
   personalized instructions AND skip step 6 entirely — no slots, no
   critique, no lift score; the account action becomes probe-first or
   deprioritize, and in the ledger Slots, Provenance audit and Critic
   context read "N/A — personalized test withheld" (the scrub then covers
   the generic email alone). If offer fit is MEDIUM or HIGH: run the
   normal controlled comparison.
   Then write the two emails, each under 120 words, with the same
   invariant structure — hook → that problem hypothesis → offer/value →
   low-commitment CTA, in that order — the same offer, and the same CTA:
   - `generic` FIRST: the strongest generally applicable version for this
     buyer role — a real problem hypothesis, role-relevant language, a
     clear value proposition — with no company-specific detail. It must be
     an email that could plausibly work on a random prospect; a weak
     strawman here invalidates the whole comparison.
   - `personalized` second: improve on that finished baseline ONLY by
     grounding it in 1–2 (not more) facts from the evidence list, naturally
     referenced — same problem, same offer components, same CTA, no new
     capabilities, no extra rhetorical moves. Rewording may only make the
     SAME underlying problem hypothesis more company-specific — never swap
     in a different problem — and only where a cited evidence item
     genuinely supports it. If the evidence is too thin for a strong
     personalized angle, say so in the output rather than manufacturing
     specificity to force a win.
   Provenance rule for the personalized email — every meaningful claim is
   supported by exactly one source: **E** (directly entailed by an
   Evidence List Observed) · **H** (an inference stated explicitly as
   uncertainty — "may", "could", "suggests", "if") · **O** (directly
   contained in the LOCKED offer claims from Input — nothing else; a
   general AI-agent offer does not permit claims about sequencing,
   qualification, reduced headcount, integrations, ROI, or vertical
   specialization unless those were locked) · **R** (generic buyer-role
   knowledge asserting nothing specific about this company). Topical
   similarity is NOT support: evidence about compliance does not permit
   the seller to claim its product keeps workflows compliant. If a
   sentence needs two unsupported bridges to become true, delete it — not
   defend it.
   Craft + style bar for BOTH emails — the floor that separates a shipped
   email from an AI-flavored one. At this length every rule is absolute
   and countable; the ledger attests the counts:
   - Subject ≤6 words. No placeholder tokens anywhere (no "[Name]", no
     "[Sender]"): open with "Hi," or the first sentence, end with a plain
     sign-off. The first sentence earns the read (never "I hope…", never
     "My name is…"). The CTA is a question answerable in one line.
   - ZERO em dashes. Commas, colons, periods instead.
   - Enumerations read like speech: "X, Y and Z" is human. ZERO comma-only
     triples ("X, Y, Z" with no "and") and ZERO stacked parallel clauses
     built for rhythm; three items joined with a plain "and" are fine.
   - ZERO negative parallelism: "it's not X, it's Y", "don't just X, Y",
     "not X. Y." in any variant. State the positive claim alone; if it
     cannot stand alone, sharpen the claim instead of propping it.
   - ZERO banned words: streamline, leverage, seamless, robust,
     transformative, game-changing, cutting-edge, unlock, empower,
     elevate, harness, revolutionize, journey, "excited to",
     "don't hesitate".
   - ZERO teased reveals or colon setups: "Here's the thing", "The
     reality:", "The result?". Say the thing.
   - ZERO empty intensifiers ("actually", "genuinely", "truly"): delete
     the word; if the sentence means the same, it stays deleted.
   The read test for every sentence: would one operator say it to another
   over lunch? If not, rewrite it. Vary sentence length; short is fine.
   The rest of the artifact (hypothesis, verdict, signal) avoids the
   banned words too, without the hard counts. The personalized subject may
   draw on the same 1–2 evidence facts, nothing more.
   **Preflight, BEFORE the critique:** check the hypothesis lock, the
   offer claim lock, the provenance rule, placeholders, word limits and
   the four-part structure. Repair ONCE, then FREEZE both emails — from
   here on nothing rewrites them, the critique included.
6. Blind critic pass — skipped entirely on the withheld branch. A separate
   evaluation pass with the email identities withheld. Precision about
   what "blind" means here, and say it this way in the output: blind to
   generation intent and to which email was meant to win; NOT blind to
   treatment identity (the critic can infer which email is personalized
   from its content) and not an independent model. For this pass, evaluate
   using ONLY: the evidence list, the buyer role, the locked offer claims,
   the written problem hypothesis (an experimental control — needed for
   the pre-score check), and both finished emails labeled "Email A" /
   "Email B" — not the step-4 hypothesis bullets, not the generation
   rationale, and not the slot-assignment rule below. Assign the slots mechanically, not by
   choice: count the letters in the company slug — even, generic is A;
   odd, personalized is A — so the generator never picks which email gets
   the favorable position. SHOW the arithmetic in the output ("slug
   `plausible` = 9 letters, odd → personalized is A"); an assignment that
   isn't shown wasn't computed. Before scoring, confirm both emails
   address the written problem hypothesis from step 5 — if one swapped in
   a different problem, the comparison is VOID (the emails are frozen;
   preflight was the repair window): a "win" produced by changing the
   problem is invalid, not a lift. Every score
   must quote a VERBATIM substring of the email or the evidence list that
   justifies it — a paraphrase is not a quote; this is an audit hook, not
   a proof of rigor. Open each email's critique by quoting that email's
   subject line: if the quoted subject does not match the email under that
   label, the labels are crossed — fix them before scoring anything.
   Score each 1–5 on:
   - **Reply likelihood** (primary) — would this buyer plausibly reply?
   - **Specificity** — genuine understanding of this company, not just an
     inserted name.
   - **Credibility** — are claims supported by the evidence without
     stretching it?
   Anchor reply likelihood with TREATMENT-NEUTRAL anchors — company
   specificity has its own score and must not be double-counted here:
   5 = compelling: strong problem–offer fit, credible claims, a clear
   reason to engage now · 4 = strong: relevant and credible enough to
   merit consideration, low-friction next step · 3 = competent but easy
   to ignore; no reason this buyer must engage now · 2 = weak relevance,
   an unsupported stretch, or template smell · 1 = spam: implausible,
   misleading, or materially fabricated. A generic email may score 4–5
   when its problem–offer fit is unusually strong; a highly personalized
   email may score 1–2 when its company-specific bridge is fabricated.
   Score against the anchors, not against the other email.
   Baseline floor: if the generic email scores ≤2 on reply likelihood, it
   failed the strawman bar — the comparison is VOID; record the void
   honestly and move on. The emails are frozen after preflight: no
   regeneration, no critique re-run.
   Personalized is allowed to lose. If the generic email's reply likelihood
   is equal or higher, say so plainly. Only after scoring, reveal the
   mapping EXPLICITLY, quoting subjects: "Email A = personalized —
   '<subject>' · Email B = generic — '<subject>'". The letters A/B exist
   ONLY inside this critique section (and the ledger's Slots row, which
   restates the mapping): everywhere else the emails are named by role —
   a reader who meets "Email A" twice with two meanings gets the verdict
   backwards on a fast read. State the verdict using reply likelihood as
   the deciding criterion, with specificity and credibility as supporting
   reasoning, not the primary claim.
7. Write `demo/output/<company-slug>.md` incrementally, not at the end.
   Every write ENDS by appending a `Status: in progress (through step N)`
   line, and earlier status lines STAY in place — they are the write log,
   the proof the file was built incrementally. The LAST line of the file
   is always its current state; the final append (after the step-9 ledger)
   is `Status: complete`, so a partial artifact can never be mistaken for
   a finished one. Create the file IMMEDIATELY after step 1 with the
   status line and the retrieval so far (each source marked **live** or
   **cached**, with dates) — the artifact must exist within seconds,
   before any reasoning; extend it
   after step 4 with the offer being tested, the evidence list, the buyer
   hypothesis bullets, and the **Offer-account bridge** sentence; append both
   emails after step 5 — labeled by ROLE ("Generic email (baseline)" /
   "Personalized email"), NEVER as "Email A/B"; the letters belong to the
   critique's blind slots alone; append the blind critique, the reveal, and the
   verdict after step 6. A run cut off partway leaves a truthful partial
   artifact on disk.
8. Close the file with a **GTM signal** block — TWO decisions, not one:
   whether this account deserves pursuit for this offer, and separately
   how much messaging effort it gets if contacted:
   - **Buyer pain hypothesis:** one sentence, explicitly a hypothesis.
   - **Evidence strength:** high = 2+ specific, traceable Observed items
     that directly support a buyer-relevant angle · medium = 1 strong or
     several weaker · low = only generic positioning. One clause on why.
   - **Offer fit:** low / medium / high (from step 4), one clause on why.
   - **Personalization result:** wins / ties / loses / WITHHELD (low-fit
     branch) on reply likelihood.
   - **Account action:** pursue / probe first / deprioritize. Low offer
     fit → probe first or deprioritize, REGARDLESS of a personalization
     win — a lift built on a manufactured bridge is contamination, not a
     reason to pursue.
   - **Messaging effort:** generic / light personalization / personalize —
     only meaningful if the account is contacted at all.
   - **Why:** one sentence combining evidence strength, offer fit, and
     the message result.
   - **Next probe:** the single cheapest public check that could CHANGE
     the account action, drawn from the hypothesis's "Missing" bullets.
   - **Winning angle:** one sentence, or exactly "None validated."
   - **Reusable hypothesis:** one sentence suggesting what could be TESTED
     across similar prospects — never stated as a learned pattern; one
     account cannot support a pattern claim, and NO numbers that are not
     in the evidence — an invented multiplier ("2x better reply rates") is
     fabrication wearing a hypothesis's clothes.
9. Append the **Run ledger** — the accountability appendix. Every row is
   an attestation WITH its evidence inline, so any drift is visible in the
   artifact itself rather than living in the agent's intentions. Compact —
   one line per row:
   - **Offer & claim lock:** given in input / derived from role before
     any fetch — quote it, and list the locked claim set the emails were
     allowed to use.
   - **Fetches:** every attempt as `URL → live | cached | failed` (one
     attempt each); then the step-3 search: run or skipped, with the
     usable-evidence count that decided it.
   - **Evidence count & offer fit:** N items (the cap is 3 — more is
     treatment inflation, not thoroughness) · offer fit with its
     one-clause reason.
   - **Slots:** slug, letter count, parity, assignment (restates step 6).
   - **Hypothesis lock:** the written problem hypothesis; then "both
     emails address it: yes/no".
   - **Provenance audit:** each meaningful personalized-email sentence
     tagged E/H/O/R with its backing (E# for evidence items). Entailment,
     not topical relatedness: "mentions compliance" does not back "we keep
     connections compliant". Any sentence outside the four categories =
     delete it from the email, or the run is invalid.
   - **Style & structure scrub:** four parts in order in both emails ·
     word counts (≤120) · subjects (≤6 words) · counted zeros on both
     emails: em dashes · comma-only triples (no "and") · stacked parallel
     clauses · banned words · negative parallelism · empty intensifiers ·
     placeholder tokens. Counts are confirmed at the step-5 preflight; a
     nonzero discovered after the freeze is recorded here as a defect,
     honestly — never silently fixed. On the withheld branch the scrub
     covers the generic email alone.
   - **Critic context:** one line — exactly what the critic pass received.
10. ONLY if the prompt explicitly asked for a page/HTML rendering — never
    otherwise, and never before the markdown is complete: render the same
    content as a small self-contained `demo/output/<company-slug>.html`
    (inline styles, zero external assets) — GTM signal block on top, then
    the score table, both emails, and the evidence chain with sources. The
    markdown IS the deliverable; the page is presentation, produced off
    the judged path.
11. Print the output path(s) and the one-sentence verdict.

## Rules

- Never send anything. Output is a reviewable draft file, nothing more.
- The buyer role is a role, not a named real person — never use an
  individual's personal data.
- If evidence is insufficient (step 1 failure with no snapshot), stop and
  say so rather than guessing.
- Never describe cached or committed input as live.
- Never manufacture company-specific detail to make personalized win. A
  result where personalized loses or ties, honestly reported, is a valid
  and useful output.

## Done when

`demo/output/<company-slug>.md` ends with `Status: complete` as its last
line, and contains: an Observed/Source evidence list, an offer claim lock,
an offer-fit rating, a buyer hypothesis that distinguishes role priors
from company evidence, an Offer-account bridge sentence (or exactly "No
strong bridge found."), ONE written problem hypothesis, then EITHER the
controlled comparison — both emails passing the craft + style bar and the
provenance rule, a blind critique with treatment-neutral anchored scores
and the slot arithmetic shown, a verdict (win, loss, or tie; VOID if the
generic baseline scored ≤2) — OR, on the low-fit branch, the generic
baseline plus "Personalized test withheld: no grounded bridge to the
offer."; and a GTM signal block ending in TWO decisions (account action ·
messaging effort) with a why, a next probe, and a reusable hypothesis, and
the Run ledger with every attestation filled, including the claim lock,
a complete provenance audit, and a style & structure scrub showing all
zero counts. The HTML rendering happens only on explicit request, never on
the judged path.
