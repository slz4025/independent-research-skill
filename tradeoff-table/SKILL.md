---
name: tradeoff-table
description: >-
  Use this skill when the user asks a decision-shaped question — "how should I do X", "what's the best way to X", "what should I get for Y", "should I use A or B", "I'm deciding between", or any question weighing options. Trigger on the shape of the question, not on stakes or tone: low-stakes, casual, or short questions ("best way to clean my bathtub") are in scope. The skill begins by asking the user whether they want a structured tradeoff table for this decision, and runs its four-step deliberation workflow (surface context, negotiate attributes, negotiate options, build a researched comparison) only on explicit yes. Skip this skill entirely (do not even ask) for purely informational questions ("how does X work"), single-step tasks ("write me a script"), or messages that already request a quick answer.
---

# Tradeoff Table

A structured workflow for thinking through decisions. The user wants to *deliberate*, not get a recommendation. The skill's value is the structure, the negotiation at each step, and the researched comparison — not a final answer. Stay in the workflow once approved. The only escape hatches are (a) the user declining the table at Step 0, or (b) the user explicitly asking for one mid-flow ("just tell me," "skip the process").

## The five steps

Step 0 is a consent gate; it must complete with explicit user approval before Step 1 runs. Steps 1–4 each end with a pause for user input.

### Step 0 — Offer the table

Before doing anything else, tell the user briefly what the tradeoff-table workflow is and ask whether they want it for this decision. The offer should be short — two or three sentences — and should name the cost as well as the benefit, so consent is informed.

A reasonable offer reads roughly: "This looks like a decision where I could either give you a quick recommendation or run a structured tradeoff table — that means I'd ask you a few questions to surface context, agree on the attributes and options together, then research the cells that warrant it and present a comparison without a final pick. It takes longer and involves several back-and-forth checkpoints. Want me to run the table, or would a quick answer be more useful?"

Use `ask_user_input_v0` with two or three options: run the table, give a quick answer, or (optionally) something in between like "give me a quick answer first, then I'll decide." Adapt the wording to the specific decision so the offer doesn't read like boilerplate.

**Hard rules at Step 0:**

- **Do not start Step 1 until the user has explicitly chosen the table.** A "yes," "do the table," "run it," or selection of the table option in `ask_user_input_v0` counts. Silence, ambiguity, "sure I guess," or "whatever you think" do not count — re-ask once with a plainer framing, and if the answer is still ambiguous, default to a quick answer.
- **On no, exit cleanly.** Do not try to smuggle the workflow in by asking "well at least let me ask one clarifying question" — that is Step 1 in disguise. Just answer the question normally.
- **Skip Step 0 entirely** (and do not invoke this skill) when the user's message already contains an explicit "quick answer" signal, when the question is purely informational, or when the question is a single-step task. Asking in those cases is friction, not consent.
- **Do not re-offer mid-flow.** Once the user has approved the table at Step 0, do not check in again at Steps 1–4 with "are you sure you want to keep going" — that's a different kind of consent gate and it undermines the deliberation. The user can exit at any time on their own initiative; the workflow does not need to keep asking.

Only after explicit approval, proceed to Step 1.

**Common failure modes at Step 0** (avoid these):

- **Skipping Step 0 because the topic feels low-stakes or domestic.** "Best way to clean my bathtub," "best way to organize my closet," "best way to water my plants" all match the trigger pattern. The user gets to decide whether the structure is worth it — that's what Step 0 is for. Do not pre-decide for them by jumping to a quick answer.
- **Skipping Step 0 because the phrasing is casual or short.** "How should I cook this?" is in scope. "What's the best way to X?" is in scope regardless of how breezy it sounds. Trigger on shape, not tone.
- **Answering first, then offering the table after.** Once you've given a quick recommendation, the offer becomes performative — the user has already started reading the answer. Step 0 must be the FIRST response, before any substantive content on the decision itself.
- **Treating the offer as optional politeness.** Step 0 is the skill. If you find yourself thinking "I'll just answer this one directly, it's obvious," that's the failure mode — invoke the skill and let the user decline if they want a quick answer.

### Step 1 — Surface context

Before proposing anything, understand the decision: constraints (budget, timeline, location, skills), stakes and reversibility, preferences and values, and any unstated assumption in the question worth surfacing (e.g., "best laundry detergent" might really be "best way to wash clothes").

If the user's message already answered some of this, don't re-ask — reflect it back and ask only about gaps. Pick the 2–3 questions that most shape what comes next; let the user volunteer more. Use `ask_user_input_v0` for discrete-answer questions; use prose for open-ended ones.

End by stating what you understood and confirming before moving on.

### Step 2 — Negotiate attributes

Start from this default set:

- **Ecological** — lifecycle impact, embodied energy, biodegradability, watershed/ecosystem effects
- **Health / safety** — toxicity, exposure routes, microbiome impact, off-gassing, allergenicity
- **Affordability** — upfront cost and per-use/lifetime cost (these often diverge)
- **Reliability / maintenance effort** — does it work consistently, and what does keeping it working require?

Treat ecological and health as separate columns by default — they often diverge (something can be ecologically benign but a skin irritant). Combine them only when the decision is low-stakes enough that the distinction wouldn't change the answer.

Add 1–2 situational attributes when warranted: effectiveness (when "does it work for *my* situation" is contested), time/effort, durability, versatility, reversibility, aesthetic, or domain-specific (soil compatibility, code compliance, tax treatment, etc.). Aim for 4–6 total. Skip a default only if truly irrelevant.

Present with one-line justifications. Ask: "Do these capture what matters? Add, remove, or reweight?"

### Step 3 — Negotiate options

Aim for **3–6 options**. Include the obvious mainstream choice (the user needs it as a baseline) and at least one option they might not have considered. No strawmen — every option must be one a thoughtful person might genuinely choose. For "what should I get" questions, options are products/categories; for "how should I do X," methods/approaches; for "A or B" questions, often A and B are starting points and the job is to surface C and D.

Present with one-line descriptions. Ask: "Anything to add, remove, or swap?"

### Step 4 — Bucket the cells, then populate

Show the user the empty table and ask which cells matter most. Sort cells into three buckets:

**Bucket 1 — `[RESEARCH]` cells (ecology, health, reliability/longevity, scientific claims).** These get the full research-skill treatment, invoked under the contract in the research skill's Composition section. Reliability/longevity belongs here whenever the claim is contested — manufacturer specs, independent testing, and user reports often diverge sharply.

1. Propose the RESEARCH cell list explicitly with one-line justifications. Wait for approval, modification, or trim.
2. For each approved cell, invoke the research skill **strictly serially**, as if the user typed `RESEARCH <question>`. Each cell is its own invocation under the Composition contract: its own Rule 2 framing checkpoint, its own Rule 2b tradition checkpoint, its own search, its own report. **Do not batch.** The Composition contract forbids it; the redundancy is the rigor.
3. Each invocation writes to `/mnt/user-data/outputs/research_<attribute>_<option>.md` (lowercase, underscore-separated) — the per-cell filename overrides the research skill's default `research_report.md`, per the Composition invariant on distinct filenames.
4. Present each file with `present_files` as it completes, so the user can review before the next cell begins. They may halt or redirect.
5. After all approved cells are done, write a 1–3 sentence distillation per cell for the table. Per the Composition contract, distillations are written by this skill (the caller), not by the research skill. They must reflect each file faithfully — including its uncertainties and conflicts — and must not introduce cross-cell synthesis.

**Bucket 2 — `[deep search]` cells (price, code/regulatory, reliability when user opts out of RESEARCH).** Multi-source web search with **real, fetched URLs** in the cell. For price, cite at least two sellers; note regional variation and date checked. For code/regulatory, link to the specific code section, not the agency homepage. For reliability handled this way, search reviews/expert evals/manufacturer specs and flag conflicts.

**Bucket 3 — `[quick take]` cells.** Short rating ("high / medium / low" or a one-phrase qualifier) plus one-sentence justification. No URL — these are acknowledged as best-effort knowledge, not research.

### Step 4b — Surface what research turned up

While populating cells, track three things and surface them to the user before finalizing the table: **emergent options** (alternatives not in the original list), **emergent attributes** (dimensions that turn out to be major differentiators), and **surprising findings** (facts that overturn an assumption or shift the framing). Don't unilaterally redo the table — ask. If yes, return to the relevant step and run targeted research. If no, note them in the caveats.

## Output format

A markdown table with options as rows and attributes as columns. Every cell carries one of the three markers:

- `[RESEARCH] *Distillation here.* ([full report](research_ecology_spray_foam.md))`
- `[deep search] Finding here. ([source1](url1), [source2](url2))`
- `[quick take] Rating — one-sentence justification.`

Below the table: caveats (what's contested, where data is thin, what could change the answer). **No final "what to pick" recommendation, even a conditional one.** The user reads the table and decides; adding a recommendation lets them skim past it to "the answer."

## Honesty rules

These keep the skill from drifting into pseudo-rigor.

- **Markers are non-optional.** Every cell carries `[RESEARCH]`, `[deep search]`, or `[quick take]`. Unmarked cells read as researched findings by default — exactly the dishonesty the markers prevent. When uncertain which bucket, default to the lower one.
- **URLs must be real and fetched.** Every URL in a `[deep search]` cell must be one you actually fetched, not constructed from training memory. If a URL doesn't load, find another or downgrade to `[quick take]`. Vague attributions like "according to Consumer Reports" without a specific URL are not citations.
- **Distillations are summaries, not new claims.** A `[RESEARCH]` cell's distillation must accurately reflect the source markdown file — including its uncertainties and conflicts. Do not introduce conclusions, syntheses, or rankings that aren't in the file. The research skill forbids cross-source synthesis; the distillation must respect that.
- **Don't overclaim natural = better or conventional = worse.** Sometimes the conventional product is the ecological choice once you account for re-doing the job, embodied energy of alternatives, or shipping. Let the research decide.
- **Don't invent specifics.** Ratios, temperatures, prices, regulations, health figures — if you're not sure, say so and give the principle, or research it.
- **Acknowledge regional and individual variation.** Hard water changes laundry. Climate changes fermentation. Local codes change building. Body chemistry changes personal care. Flag where it matters.

## Worked example — the bucketing decision

For "What's the best way to insulate my detached workshop?" with attributes [ecological, health, affordability, reliability/longevity, R-value per inch, fire performance] and options [fiberglass, mineral wool, cellulose, closed-cell spray foam, rigid foam, wood fiber, sheep wool], a reasonable bucketing might be:

- `[RESEARCH]`: ecological and health columns for cellulose/spray foam/fiberglass; reliability/longevity for cellulose and spray foam (both have contested 30-year track records).
- `[deep search]`: affordability column (current installed prices, regional, with seller URLs).
- `[quick take]`: R-value per inch (well-established specs), fire performance (standard ratings).

That's ~6 RESEARCH cells, 7 deep-search cells, 14 quick takes. Propose to the user, get approval, run the RESEARCH cells one at a time with full per-cell checkpoints, fill the rest, present the table.
