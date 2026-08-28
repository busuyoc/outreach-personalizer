---
name: outreach-personalizer
description: Given a company URL and a buyer role, fetches the company's real public site, simulates that buyer role's reaction, blind-tests whether evidence-grounded outreach beats a strong generic baseline on simulated reply likelihood, and distills the result into an actionable GTM signal — buyer pain hypothesis, evidence strength, winning message angle. Use when the user names a company or URL and a buyer role and asks for personalized outreach, a pitch, message testing, or GTM signal on a prospect.
---

# Outreach personalizer

What this skill actually tests: whether an evidence-grounded personalization
workflow beats a *strong* generic baseline on a simulated reply-likelihood
signal — not whether personalized beats a deliberately weak strawman, and not
a general claim that "personalization works." The score is a cheap,
inspectable ranking signal for deciding what to say to THIS account — not a
prediction of real reply rates. The deliverable is the GTM signal block at
the end (pain hypothesis, evidence strength, winning angle, reusable
signal), with the emails and the blind scores as its audit trail. If the
generic email is weak, the result proves nothing.

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

## Steps

1. Fetch the company's homepage — one attempt, never retry. Record the exact
   URL and today's date as the retrieval date. If the fetch fails or is slow
   (~15s), fall back to the cached snapshot in `demo/input/` for this company
   if one exists, and say so explicitly — never describe cached content as
   live. If there is no live fetch and no cached snapshot, report
   "insufficient evidence: could not reach `<url>`" and stop.
2. One homepage is thin. Fetch exactly one more page, directly linked from
   the homepage: take the highest-priority category present — comparison,
   then pricing, then product/use-case, then about — and within that
   category the first directly linked URL in document order (~10s budget,
   one attempt, never retry). Fetch it only if it plausibly adds at least
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
4. Build a short evidence list from what was actually fetched: 2–3 items
   when the pages support them, honestly fewer when they don't — never
   lower the specificity bar to hit a count. A usable item is one a
   reviewer can trace to a concrete sourced statement ("they are a SaaS
   company" is not one), and it states what the page SAYS — any "this
   signals/suggests…" reading is inference and belongs in the hypothesis
   bullets, never in the evidence list. For company-specific claims use ONLY this evidence
   list — ignore anything already known about this company from training
   data; a famous company earns its evidence the same way as an unknown
   one. If the second page was a comparison page, one evidence item should
   capture the dimension the company differentiates on — that positioning
   signal is usually the safest personalization hook. Then build a
   role-grounded buyer hypothesis (this is a hypothesis about a role, not a
   simulation of a real person) for the offer being pitched (given or
   derived — see Input), in compact bullets, keeping three things distinct:
   - **Role prior** (1–2 bullets) — what this kind of buyer typically cares
     about, independent of this company; never counts as company evidence.
   - **Evidence-backed concerns** (1–3 bullets) — where a cited evidence
     item makes a role-typical concern especially likely here.
   - **Missing** (1–2 bullets) — what you'd want to know but don't.
   An inference may inform the hypothesis, but must never be phrased as a
   known company fact unless the evidence explicitly states it. Close the
   section with **Why this account** — exactly ONE sentence, hard limit:
   an evidence-backed hypothesis for why this company's public positioning
   makes the offer potentially relevant now — "their positioning suggests
   X may matter", never "they need X". Do not invent facts not present in
   the sources.
5. First write down THE problem hypothesis: one role-level sentence that
   BOTH emails must address, printed in the output above the emails. Then
   write the two emails, each under 120 words, with the same invariant
   structure — hook → that problem hypothesis → offer/value →
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
6. Blind critic pass — a separate evaluation pass with the email identities
   withheld. Precision about what "blind" means here, and say it this way
   in the output: blind to generation intent and to which email was meant
   to win; NOT blind to treatment identity (the critic can infer which
   email is personalized from its content) and not an independent model. The
   critic's context contains only the evidence list, the buyer role, the
   offer, and both finished emails labeled "Email A" / "Email B" — not the
   step-4 objections, not the generation rationale, and not the
   slot-assignment rule below. Assign the slots mechanically, not by
   choice: count the letters in the company slug — even, generic is A;
   odd, personalized is A — so the generator never picks which email gets
   the favorable position. SHOW the arithmetic in the output ("slug
   `plausible` = 9 letters, odd → personalized is A"); an assignment that
   isn't shown wasn't computed. Before scoring, confirm both emails
   address the written problem hypothesis from step 5 — if one swapped in
   a different problem, stop and rewrite that email: a "win" produced by
   changing the problem is an invalid comparison, not a lift. Every score
   must quote a VERBATIM substring of the email or the evidence list that
   justifies it — a paraphrase is not a quote; this is an audit hook, not
   a proof of rigor. Open each email's critique by quoting that email's
   subject line: if the quoted subject does not match the email under that
   label, the labels are crossed — fix them before scoring anything.
   Score each 1–5 on:
   - **Reply likelihood** (primary) — would this buyer plausibly reply?
   - **Specificity** — genuine understanding of this company, not just a
     inserted name.
   - **Credibility** — are claims supported by the evidence without
     stretching it?
   Anchor reply likelihood so scores don't cluster at 3–4: 5 = names a
   problem this buyer has publicly committed to — replying is the path of
   least resistance · 4 = specific and credible enough that ignoring it
   costs the buyer something · 3 = competent; could go unchanged to a
   dozen similar companies · 2 = template smell or one stretched claim ·
   1 = spam. Score against the anchors, not against the other email.
   Baseline floor: if the generic email scores ≤2 on reply likelihood, it
   failed the strawman bar — the comparison is VOID; rewrite the generic
   email and re-run the critique rather than banking a hollow win.
   Personalized is allowed to lose. If the generic email's reply likelihood
   is equal or higher, say so plainly. Only after scoring, reveal which
   label was which. State the verdict using reply likelihood as the
   deciding criterion, with specificity and credibility as supporting
   reasoning, not the primary claim.
7. Write `demo/output/<company-slug>.md` incrementally, not at the end.
   Every write ENDS by appending a `Status: in progress (through step N)`
   line, and earlier status lines STAY in place — they are the write log,
   the proof the file was built incrementally. The LAST line of the file
   is always its current state; the final append (after the step-9 ledger)
   is `Status: complete`, so a partial artifact can never be mistaken for
   a finished one. Create the
   file IMMEDIATELY after step 1 with the status line and the retrieval so
   far (each source marked **live** or **cached**, with dates) — the
   artifact must exist within seconds, before any reasoning; extend it
   after step 4 with the offer being tested, the evidence list, the buyer
   hypothesis bullets, and the **Why this account** sentence; append both
   emails after step 5; append the blind critique, the reveal, and the
   verdict after step 6. A run cut off partway leaves a truthful partial
   artifact on disk.
8. Close the file with a **GTM signal** block — the decision a growth team
   can act on beyond this one email:
   - **Buyer pain hypothesis:** one sentence.
   - **Evidence strength:** high = 2+ specific, traceable claims that
     directly support a buyer-relevant angle · medium = 1 strong claim or
     several weaker relevant ones · low = only generic positioning. One
     clause on why.
   - **Personalization lift:** wins / ties / loses on reply likelihood.
   - **Recommended effort:** derived from the two lines above —
     personalize (high evidence, positive lift) · light personalization
     (medium evidence or thin lift) · don't spend research time here, use
     the strong generic (low evidence — that answer is itself the value).
   - **Next probe:** one sentence — the single cheapest check that would
     most raise evidence confidence, drawn from the hypothesis's "Missing"
     bullets (e.g. "check their changelog for team-features velocity").
     This is what makes "go light" and "don't personalize" actionable
     rather than dead ends.
   - **Winning angle:** one sentence naming the angle that won, or why
     none did.
   - **Reusable hypothesis:** one sentence suggesting what could be TESTED
     across similar prospects — phrased as a hypothesis to test, never as
     a validated cross-account pattern; one account cannot support a
     pattern claim, and the hypothesis may contain NO numbers that are not
     in the evidence — an invented multiplier ("2x better reply rates") is
     fabrication wearing a hypothesis's clothes.
9. Append the **Run ledger** — the accountability appendix. Every row is
   an attestation WITH its evidence inline, so any drift is visible in the
   artifact itself rather than living in the agent's intentions. Compact —
   one line per row:
   - **Offer:** given in input / derived from role before any fetch —
     quote it.
   - **Fetches:** every attempt as `URL → live | cached | failed` (one
     attempt each); then the step-3 search: run or skipped, with the
     usable-evidence count that decided it.
   - **Evidence count:** N items (the cap is 3 — more is treatment
     inflation, not thoroughness; trim to the 3 strongest).
   - **Slots:** slug, letter count, parity, assignment (restates step 6).
   - **Hypothesis lock:** the written problem hypothesis; then "both
     emails address it: yes/no".
   - **Structure:** four parts present in order in both emails; word
     count of each (must be ≤120); subjects ≤6 words.
   - **Style scrub:** counted on both emails — em dashes: 0 · comma-only
     triples (no "and"): 0 · stacked parallel clauses: 0 · banned words:
     0 · negative parallelism: 0 · empty intensifiers: 0. Any nonzero
     means rewrite BEFORE the critique, then re-count; a critique run on
     unscrubbed emails is invalid.
   - **Fact audit:** every company-specific claim in the personalized
     email → the evidence item number that backs it. A claim with no row
     is a defect: delete the claim from the email, or the run is invalid.
   - **Critic context:** exactly what the critic pass received.
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
line, and contains: a sourced evidence list, a buyer hypothesis that
distinguishes role priors from company evidence, a "Why this account"
sentence, ONE written problem hypothesis that both emails demonstrably
address, both emails passing the craft bar, a blind critique of each with
anchored scores and the slot arithmetic shown, a verdict — win, loss, or
tie — decided on reply likelihood and explained honestly (VOID if the
generic baseline scored ≤2), a GTM signal block ending in a
recommended-effort decision, a next probe, and a reusable hypothesis, and
the Run ledger with every attestation filled, including a complete fact
audit and a style scrub showing all zero counts. The HTML rendering
happens only on explicit request, never on the judged path.
