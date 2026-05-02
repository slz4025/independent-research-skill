---
name: research
description: "Use this skill in two cases only: (1) when the user explicitly begins their message with the word \"RESEARCH\" in all caps, followed by their question (e.g., \"RESEARCH does coffee cause cancer?\"); or (2) when another skill explicitly invokes this skill as a sub-procedure under the contract in the Composition section below. Do not trigger on any other phrasing, no matter how similar the intent."
---

# Research Skill

Find and present what research and firsthand sources say on the given question. The user does the synthesis; the skill assembles the inputs. Report uncomfortable findings directly; do not soften, omit, or hedge beyond what the evidence warrants. No emojis. No padding.

Use natural, human-toned language throughout. Write as a thoughtful person summarizing what each source says, not an academic or a robot.

---

## Composition — invocation by other skills

This skill is designed to be callable as a sub-procedure by other skills, not just as a top-level response to the `RESEARCH` keyword. A calling skill may invoke this one — typically per-question or per-cell within a larger workflow — provided it preserves the invariants below. Direct user invocation still requires the all-caps `RESEARCH` keyword; this section authorizes skill-to-skill calls only.

**Invariants the caller must preserve (non-negotiable):**

1. **One framed question per invocation.** Each call corresponds to a single research question with its own Rule 2 framing (population, outcome, time horizon, term operationalization, contested premises). Callers may not bundle multiple questions into one invocation to save checkpoints.

2. **Per-invocation Rule 2 and Rule 2b checkpoints.** Every invocation runs the framed-question confirmation (Rule 2) and the tradition-coverage confirmation (Rule 2b) with the user, in that order, before any searching. The caller does not get to skip these on the grounds that an earlier invocation in the same workflow already ran them. The redundancy is the rigor — different questions warrant different framings and different traditions.

3. **No batching, no parallel invocations.** Invocations run strictly serially. A caller running multiple research sub-procedures in one workflow runs them one at a time, with full checkpoints for each, and presents each output before starting the next. The user may halt or redirect between invocations.

4. **All Empirical and Explore rules apply unchanged.** Source-text fidelity, prior-blind searching, WEIRD audit, cultural-default audit, no-synthesis, SHA-256 ordering — none of these are relaxed because the call is sub-procedural. A research output produced inside another workflow is held to the same standard as one produced from a direct `RESEARCH` keyword call.

5. **Each invocation produces its own file.** Output goes to `/mnt/user-data/outputs/`. The caller specifies the filename; the default `research_report.md` is overridden when multiple invocations are expected, to avoid clobbering. Each file is presented with `present_files` as it completes.

6. **Distillations belong to the caller, not this skill.** If the caller wants a short summary of the report for use elsewhere (e.g., a table cell, an executive summary, a recommendation), the caller writes that distillation from the produced file. This skill produces the file; it does not produce summaries of itself. Distillations written by the caller must reflect the file faithfully, including its uncertainties and conflicts, and must not introduce cross-source synthesis that the file itself forbids (per Both-Sections Rule 1).

**What a caller looks like in practice:**

A calling skill — for example, a decision-deliberation skill running researched cells in a comparison table — invokes this skill as if the user had typed `RESEARCH <question>` for each cell. The caller is responsible for:

- Deciding which questions warrant the full procedure (vs. lighter-weight search).
- Naming each output file distinctly.
- Pausing for the user between invocations.
- Writing any cross-cell or cross-question synthesis itself, after the fact, from the produced files.

**Authorized callers as of this version:** `tradeoff-table` (invokes per `[RESEARCH]` cell at its Step 4). New caller skills may be added; each must honor the invariants above.

---

## Rules — Both Sections

**1. No synthesis.**
Present what each source says. Do not integrate findings across sources, do not rank conflicting claims by which is "more likely true," do not produce a "bottom line." Each finding stands alone under its own citation. The user does the synthesis.

Do not write: "the evidence suggests," "on balance," "taken together," "the weight of evidence," "most studies find," "while X, Y is more compelling," "the stronger interpretation," "this is contested but," or any sentence whose subject is "the literature" or "the research." If two sources conflict, present both. The Quality tier on each source is the only comparative signal; do not editorialize on top of it.

**2. Agree on the research question before searching.**
Before any search, identify: (a) the population in scope (general adult population? a specific subgroup? a specific country or era?), (b) the outcome measure (if the question implies one — e.g., "does X work" needs to specify "work for what"), (c) the time horizon (short-term vs long-term effects often diverge), (d) the operationalization of any contested term in the question, (e) any contested premise the question rests on — flag it; do not silently reshape the question to avoid it.

Write up the proposed research question with these dimensions made explicit, including any flagged premise issues, and present it to the user. Wait for the user to confirm or adjust before proceeding. The user may keep the original framing, refine it, or split it into multiple questions. Confirmation is mandatory regardless of how well-formed the question already appears — do not skip this step on the judgment that the question is clear enough.

After the user confirms the framed question, proceed to Rule 2b (tradition confirmation) before any searching.

**2b. Agree on tradition coverage before searching.**
After the user confirms the framed question (Rule 2), and before any searching, propose the interpretive traditions you intend to cover. List the major traditions that bear on the question (e.g., quantitative-empirical, critical-theoretical, classical-liberal/libertarian, religious or traditionalist, lived-experience-from-affected-population). For each proposed tradition, name the conclusion that tradition typically reaches on questions of this kind. The proposed list must satisfy the anti-correlation requirement in Rule 4 — at least one tradition must historically reach different conclusions from at least one other.

If lived-experience-from-affected-population is among the traditions to be covered, also identify at this step the *cultural default(s)* relevant to the question, per Explore Rule 5. State which arrangement, choice, or position the surrounding culture treats as standard or unmarked, and note any cases where the relevant default differs across populations the question covers. The user may challenge the model's read of what counts as the default.

Present this proposal to the user. The user may add traditions, remove traditions, challenge your characterization of what a tradition typically concludes, challenge the cultural-default identification, or ask you to substitute one tradition for another. Wait for the user to give explicit approval of the final tradition list (and cultural-default identification, if applicable) before searching. Confirmation is mandatory; do not skip on the judgment that the proposal is obviously adequate.

The approved tradition list and cultural-default identification are recorded verbatim in the Methodology section of the final report.

**3. Full breadth; actively seek contradicting evidence.**
Search exhaustively. For every finding, search for sources that contradict it. Include sources across the ideological spectrum (see Rule 4 on tradition coverage). If two quality sources conflict, present both without resolving the tension.

**4. Tradition coverage substance and non-English sources.**
The traditions agreed in Rule 2b are the coverage commitment for the rest of the research. The relevant traditions vary by question — monetary policy questions may not need feminist theory; family-structure questions do.

**Anti-correlation requirement.** The traditions covered must include at least one that historically reaches *different conclusions* from the others on questions of this type. Three traditions that converge on the same answer (e.g., quantitative-empirical, classical-liberal, and rationalist-Bayesian on many social questions) do not satisfy this rule. This is enforced at the Rule 2b confirmation step.

Explicitly include non-English sources where they bear on the question. Search non-English research databases, regional academic journals, and non-English-language essays, polls, and forums when the question has a cross-cultural dimension or when a non-Anglophone tradition has done substantial work on it. Evaluate non-English sources by the same methodological criteria as English ones — not downgraded for being non-English, not upgraded as token cross-cultural inclusion. Note source language in the metadata.

For non-English sources, quote the original-language phrase alongside the English translation for *every* cited finding. If the translation cannot be verified to a confident standard, do not include the source — note in the Methodology section that the source was found and excluded for translation-verification reasons, with enough detail (database, author or title, language) that the user can pursue it manually if they wish.

**5. Prior-blind searching.**
Search queries must be constructed from the parameters of the framed research question (Rule 2) and the approved traditions (Rule 2b) — *not* from the model's prior knowledge of which authors, studies, programs, or papers are landmark in the field. Specifically:

- Queries may not contain author surnames, study acronyms, program names, or specific paper titles drawn from the model's training-data prior. The query "Belsky daycare attachment" or "NICHD SECCYD age 15" is a methodology violation. The query "early non-maternal care attachment outcomes longitudinal" or "infant daycare hours behavior problems meta-analysis" is correct.
- Author names, study acronyms, and paper titles may enter follow-up queries *only* after a search has already surfaced them. Citation-trail searching (looking up references found in already-fetched sources) is not a violation; it is the prior-blind search succeeding.
- The reason for this rule: searching by prior knowledge biases coverage toward studies that are already canonical in the model's training data, which is a non-trivial subset of the actual literature, skewed toward English-language, well-cited, contemporary-mainstream work. The bias is invisible to the user unless explicitly disclosed, and it competes with the breadth requirement in Rule 3 and the tradition-coverage requirement in Rule 4.
- The model may, however, use its prior knowledge to *identify gaps* — for example, noticing during search-result review that a known landmark study did not surface and adding a topic-only query designed to find the type of study it represents. The query must still be parameter-based, not name-based.

The first-wave search strategy (the queries used before any results were returned) is recorded in the Methodology section, with a statement confirming that no prior-knowledge author/study names appeared in those queries.

---

## Rules — Empirical Research

**1. Source-text fidelity.**
Every claim, citation, and metadata detail must come from text actually fetched and read — never from training memory. Specifically:

- **Primary sources only.** If a secondary source references a primary study, find and cite the primary directly. Only cite a secondary source if the primary is genuinely unfindable; flag this in the quality rating.
- **Verify URLs and DOIs.** Every URL or DOI must resolve to the cited work before inclusion (check DOIs via https://doi.org/[DOI]); flag unverifiable URLs and downgrade. If only abstract/metadata was accessible, append "; abstract only, full text paywalled" to the Methodology sub-bullet and scope findings strictly to what the abstract states.
- **Confirm metadata.** Author names, journal names, sample sizes, study dates, and methodology details must be confirmed from the fetched source. If a specific detail (e.g. exact N, publication year) cannot be confirmed, append "; unverified" to that detail. Never invent or assume metadata.
- **Cross-references must be explicit.** Only write "[N] challenges [M]" or "[N] supports [M]" if the relationship is explicitly stated or directly demonstrable from the fetched text of source [N]. Do not infer cross-relationships from topic similarity. If the relationship is your inference, flag it: "(inferred; not explicitly stated in source)".
- **Critique/limitation sourcing.** Every Critique/Limitations bullet must trace to (a) text in the source itself, (b) text in another fetched source that explicitly names this source, or (c) a clearly identifiable best-practice gap (e.g. "no control group", "WEIRD sample"). Do not write limitations from memory or general impression. If uncertain whether a limitation is real, omit it.

**2. WEIRD as a sample-composition concern.**
Studies drawing exclusively from WEIRD populations (Western, Educated, Industrialized, Rich, Democratic) — especially US undergraduate convenience samples — often have a generalizability problem that bears on the Sample dimension of the quality rating. This is a concern to flag, not a mechanical one-tier downgrade. When the WEIRD sample is the population the question is about (e.g., a question about US college students), it is not a generalizability problem and should not count against the Sample rating. When the question implies broader generalization, factor the WEIRD limitation into the Sample dimension and note it explicitly. Cross-cultural or population-representative samples are a positive signal on the Sample dimension.

**Sample composition reporting.** For every empirical source, the Methodology sub-bullet must include explicit sample composition: % WEIRD or non-WEIRD if reported, % undergraduate if relevant, country breakdown if available, age range, and any other dimension on which the sample's representativeness for the claim can be assessed. If the source does not report this, write "sample composition not reported" — do not infer or assume. Whether the WEIRD limitation applies must be traceable to the reported composition and the question's scope, not to model judgment.

**3. Critiques and limitations.**
For each empirical source, actively search for limitations the source acknowledges, sources that directly critique it, and best-practice gaps (including WEIRD-sample concerns where they apply per Rule 2). Include a Critique/Limitations sub-bullet only if one applies. Critique sourcing follows Rule 1.

**4. Handle empty empirical results gracefully.**
If after searching every Empirical aggregator no studies meet at least Moderate quality, write under "## Empirical Research" the single line: "*No studies meeting quality criteria were found for this question. See Explore section.*" Do not pad with weak citations to fill the form. Do not interrupt the user. Proceed to the Explore section.

---

## Rules — Explore

**1. Search all source types actively.**
Search every Explore category defined in the Sources section. Prioritize ideological, cultural, disciplinary, and temporal diversity. Do not default to contemporary English-language sources; historical texts, non-Western perspectives, and minority viewpoints are all in scope.

**2. Locate sources by tradition, not by reputation.**
Per Rule 4 (Both Sections), search for sources active in each interpretive tradition relevant to the question. Where a category lists named institutional references (e.g., SEP, JSTOR, Pew, university OCW), use them as structural search tools. Where a category does not name specific outlets, this is deliberate — locate the substantive writers in each relevant tradition for the specific question. The goal is range of interpretation and lived experience, not comprehensiveness of evidence.

**3. No critique framing for Explore sources.**
Do not apply academic quality criteria or critique/limitations framing to Explore sources. Instead, note Context (author position, platform, audience, blind spots) and rate Noteworthiness using the following five dimensions:
- **Detail and depth**: How specific, thorough, and substantive is the argument or account?
- **Self-reflection**: Does the author examine their own assumptions, biases, or blind spots?
- **Originality of framing**: Does it offer a perspective or interpretation not found in the empirical literature?
- **Imaginative range**: Does it make unexpected connections, reframe the question, or illuminate it from an unusual angle?
- **Lived specificity**: For experiential sources, how concrete and particular is the account (vs. generic)?

Rate as: **Remarkable** (strong on most dimensions), **Noteworthy** (strong on some), or **Routine** (weak on most). Give a one-sentence reason.

**4. Explore sources do not need to be recent.**
A 19th-century essay, a 1970s documentary, or a classic work of social theory is as valid as a recent blog post if it illuminates the question in a way current sources do not.

**5. Cultural-default audit for lived-experience searches.**
When searching for accounts that cut against a speaker's prior expectations, the model must distinguish two things and search them separately:

(a) The *cultural default* — the arrangement, choice, or position that the surrounding culture treats as standard, expected, or unmarked.

(b) Each *individual speaker's prior* — what that specific person expected for themselves, which may match or diverge from the cultural default.

The model must search lived-experience accounts in *both* directions of divergence between (a) and (b). Specifically:

- Speakers whose prior matched the cultural default and whose experience cut against it.
- Speakers whose prior matched the counter-cultural arrangement and whose experience cut against it.

Asymmetric search effort across these directions — for example, searching only for accounts that cut against the culturally-default-aligned speakers, or only against the counter-cultural ones — is a methodology violation. It is not a sourcing judgment based on availability; thinness of results in one direction is a finding to report, not a reason to skip the search.

Before searching, the model identifies the cultural default(s) relevant to the question and names them in the search plan presented during the Rule 2b confirmation step. The user may challenge the model's identification of what counts as the cultural default. Defaults often differ across the populations the question covers (e.g., maternal employment is the cultural default in some communities and counter-cultural in others); when this is the case, the model searches each relevant default separately rather than collapsing them.

The cultural defaults identified, and the search directions taken against each, are recorded in the Methodology section of the final report.

---

## Sources

Sources split into two categories that appear as separate sections in the output. The split is by what the source *does*, not where it was published:

**Empirical Research**: Sources reporting original empirical work — original data collection (experiments, surveys, observational studies), original quantitative analysis of existing data (re-analyses, replications, meta-analyses, statistical analyses of public datasets), or rigorous direct measurement. A pre-registered independent replication on a personal website goes here. A meta-analysis published in a journal goes here. A well-conducted Substack post that runs original regression analysis on a public dataset goes here. A peer-reviewed essay that interprets other people's findings without doing original analysis goes in Explore. Ranked by methodological quality (Excellent first).

**Explore**: All sources that interpret, frame, argue, or report lived experience without doing original empirical work. Includes Theoretical & Analytical Essays, Analytical Blogs & Essays (where the post is interpretive rather than original analysis), Polls & Surveys (informal/community polls — formal survey research with documented methodology goes in Empirical), Public Forums & Anecdotal Accounts, and Videos. Ranked by Noteworthiness. These illuminate, interpret, reframe, or offer lived experience; they are not evaluated as evidence.

**Migration:** When a source previously routed to Explore (e.g., a Substack post) turns out to do original empirical work to a documented standard, move it to Empirical and rate it on the Quality criteria. Conversely, a peer-reviewed publication that does no original empirical work belongs in Explore — venue does not override what the source actually does.

---

### 1. Empirical Studies

Includes academic publications, working papers, pre-prints, and rigorous independent empirical work outside academia. Evaluated on methodological merit, not on venue (see Quality criteria below).

**Search strategy:** Search all major aggregators below, using their filters and sort options actively. Do not rely on a single source. Prefer open-access or PMC-hosted full text; for paywalled sources, use abstract only and flag it. **Preprint or working-paper status is provenance, not a quality downgrade — evaluate on methodology.**

- **PubMed** (https://pubmed.ncbi.nlm.nih.gov): Health, psychology, psychiatry, epidemiology, neuroscience. Use publication-type filters: `&filter=pubt.meta-analysis`, `&filter=pubt.randomizedcontrolledtrial`, `&filter=pubt.systematicreview`. Filter by date; sort by Best Match or Most Recent.
- **Semantic Scholar** (https://www.semanticscholar.org): Psychology, social science, interdisciplinary. Filter by minimum citation count, year, and field of study. Use to find highly-cited papers as anchors, then find their critics.
- **Google Scholar** (https://scholar.google.com): Broadest coverage; economics, sociology, evolutionary psychology, education, political science, anything not well-indexed elsewhere. Sort by date for recent work; use `cited by` links on landmark papers to find critiques and replications.
- **PubMed Central / PMC** (https://pmc.ncbi.nlm.nih.gov): Free full-text archive; prioritise PMC-hosted papers for full-text access.
- **SSRN** (https://ssrn.com): Economics, finance, law, social science working papers.
- **PsyArXiv** (https://psyarxiv.com): Psychology preprints.
- **bioRxiv** (https://biorxiv.org): Biology and neuroscience preprints.
- **JSTOR** (https://www.jstor.org): Humanities, sociology, history, anthropology. Good for older foundational work.

**Non-English empirical literature:** For questions with cross-cultural dimensions or where non-Anglophone research traditions have done substantial work, search non-English databases and journals. Examples: J-STAGE (Japan), CNKI (China), SciELO (Latin America/Iberia, often open-access), CAIRN (French-language social sciences), Persée (French humanities), DOAJ (multilingual open-access). Search in the source language where the model can do so reliably; otherwise search for non-English authors and translated abstracts in English. Record source language in Provenance.

**Non-academic empirical work:** In scope when it reports original data collection or analysis with documented methodology. Examples: government statistical agencies and longitudinal surveys (BLS, ONS, Eurostat, national census data), think-tank empirical reports with public methodology, independent researchers running pre-registered studies on personal websites or Substacks, replication projects run by independent groups.

For each sub-domain of the research question, run at least one targeted search per relevant aggregator. Do not stop at the first supportive result from a single source.

**Format:**

### [N]
Finding framed as closely as possible to how the authors themselves state it. No methodological details here; those go in sub-bullets. If the authors' framing carries interpretive assumptions, reproduce it faithfully and note the assumption in Critique/Limitations.
  - Source: [Source](URL)
  - Data: [What the data concretely shows; include key statistics, effect sizes, p-values, confidence intervals, or correlations as reported. If abstract only, note what is available.]
  - Methodology: [study type, sample size, key design features, sample composition (% WEIRD or non-WEIRD if reported, % undergraduate, country breakdown, age range, etc. — write "sample composition not reported" if the source does not state it); append "; abstract only, full text paywalled" if applicable]
  - Provenance:
    - Author(s): [name(s) and affiliation(s)]
    - Funding: [funding source(s); "self-funded" or "independent" if applicable; "not disclosed" if not stated]
    - Conflicts of interest: [any relevant; "none disclosed" if none stated]
    - Venue: [journal, preprint server, working paper series, personal website, etc. — factual context only, not a quality signal]
    - Source language: [if non-English]
  - Critique/Limitations: (omit if none apply)
    - [one bullet per point; cross-reference other citations as [[M]](#m) where m is the lowercase number]
  - **Quality dimensions:**
    - Design: [strong / solid / limited / major flaw — name the design type]
    - Sample: [adequate / WEIRD-limited / small / unrepresentative — reference Methodology]
    - Replication: [independently replicated / not yet replicated / known failure(s)]
    - Conflict of interest: [none / disclosed and controlled / unaddressed]
    - Pre-registration & data availability: [pre-registered with public data/code / partially / methods not inspectable]
  - **Quality:** [Excellent / Good / Moderate / Weak] — must follow mechanically from the weakest qualifying dimension above; no separate prose explanation.

**Quality criteria — methodology, not institution:**

Quality is rated on methodological merit, not on where a study was published or who funded it. Peer review is not a quality signal in this skill — peer-reviewed work passes weak studies regularly and rigorous work routinely circulates outside peer review. A well-designed pre-registered study run by an independent researcher counts the same as one in a top journal; a poorly designed convenience-sample study in Nature is rated by its design.

Each empirical source is rated on five methodological dimensions, reported explicitly in the Format block before the tier:

- **Design**: RCT > longitudinal cohort > matched observational > cross-sectional correlational > convenience-sample correlational. Pre-registration counts as part of the design rating.
- **Sample**: size and representativeness for the claim; WEIRD composition per the sample composition reporting rule.
- **Replication**: independently replicated by a separate group with independent data is the strongest signal. Replication does not require academic publication.
- **Conflict of interest**: independent funding, conflicted funding (e.g., industry-funded study showing the industry's product works), or COI controlled for in design.
- **Pre-registration and data availability**: pre-registered with public data and code, partial, or methods not inspectable.

**Tier mapping (mechanical, not subjective):**

- **Excellent**: Strong Design (RCT, longitudinal, or pre-registered), adequate Sample, independently replicated, no unaddressed COI, methods inspectable.
- **Good**: Solid Design, adequate Sample, not yet replicated but no failures, no unaddressed COI, methods inspectable.
- **Moderate**: Limited on at least one of Design (cross-sectional, weak controls), Sample (small N, WEIRD where generalizability matters), or Replication (one independent failure). May have unaddressed COI.
- **Weak**: Major flaw on at least one dimension: Design (severely confounded), Sample (very small or unrepresentative), Replication (multiple known failures), COI (strong unaddressed), or methods not inspectable.

A source is rated by its weakest qualifying dimension — a study with Excellent Design but Weak Sample is Moderate or Weak, not Excellent. This prevents tier inflation by averaging.

---

## Shared Format — All Explore Source Types

Theoretical Essays, Analytical Blogs, Polls & Surveys, Public Forums, and Videos all use one shared entry format. (Empirical Studies above has its own format because it carries the full evidence-evaluation structure.) The Attribution field's label and content depend on source type; some fields are conditional and only used by certain source types.

```
### [N]
[Argument, claim, finding, or account framed as closely as possible to how the source states it. For forums, use direct words where short quotes serve clarity. For videos with available transcripts, use direct quotes.]
  - Source: [Source](URL)
  - Attribution: [label and content depend on source type:]
      - Theoretical Essays → Tradition: [Marxist / feminist / evolutionary psychology / postcolonial / libertarian / religious / etc.]
      - Analytical Blogs → Author: [name and relevant credentials or context if identifiable]
      - Polls & Surveys → Pollster: [name of researcher or organization conducting the poll]
      - Public Forums → Community: [subreddit or forum name and relevant context]
      - Videos → Creator: [channel name, credentials if relevant]
  - Data: [Polls only — key figures, percentages, or statistics as reported]
  - Sample: [Polls only — N respondents, platform, date if available]
  - Transcript: [Videos only — Accessed / Partially accessed / Unavailable]
  - Context: [Source's platform, audience, apparent motivation or perspective, and any obvious blind spots. For Theoretical Essays, also note intellectual tradition. For Forums, note platform selection dynamics, community skew, anonymity level. For Polls, note who is likely over- or under-represented.]
  - **Noteworthiness:** [Remarkable / Noteworthy / Routine]. [One sentence reason; see Explore Rule 3 for dimensions.]
```

Conditional fields (Data, Sample, Transcript) appear only for the source types that use them; omit otherwise. Attribution is mandatory for all five source types but its label depends on the type.

---

### 2. Theoretical & Analytical Essays

Works that offer original frameworks, arguments, or interpretations; canonical texts (Marx, feminist theory, evolutionary psychology), academic essays, and substantive public intellectual writing. Cited for argument quality and influence, not data.

**Search strategy:** Per Rule 4 (Both Sections), first identify the interpretive traditions that bear on the question, then locate essays and analytical writing within each. Cast widely across time, tradition, and ideological perspective. Historical texts are in scope.

*Reference sources to use across all traditions:* Stanford Encyclopedia of Philosophy (plato.stanford.edu) for philosophical framing of the question, JSTOR for older humanities essays, Google Scholar for citation trails into theoretical literatures.

Named outlets are deliberately omitted to avoid anchoring search to one ideological cluster. Locate the substantive analytical writing within each tradition relevant to the question — including canonical historical works (e.g., classical social theory, foundational texts in any relevant tradition), academic essays, and serious public intellectual writing. A conservative religious essayist and a Marxist feminist may both illuminate the same empirical finding from opposite directions; both are worth including where their tradition bears on the question.

Uses the shared Explore format above (Attribution = Tradition).

---

### 3. Analytical Blogs & Essays

Substack posts, Medium articles, personal website essays with substantive analysis or original interpretation. Sources that interpret or argue rather than report original empirical work. Quality standard to aim for: writers with transparent reasoning, domain expertise, willingness to engage critics, and a track record on the question.

**Search strategy:** Per Rule 4 (Both Sections), first identify the interpretive traditions that bear on the question, then find blogs or essayists active in each. Search topic + "substack" or "medium" or "essay" or "blog"; also search for known domain experts who publish outside peer review, and for serious writers in traditions that academic search will under-represent.

Named outlets are deliberately omitted to avoid anchoring the search to one cluster. The model should locate the actual quality writers in each relevant tradition for the specific question being researched, including traditions whose writers are less search-engine-discoverable (older essays, smaller publications, non-English sources).

Uses the shared Explore format above (Attribution = Author).

---

### 4. Polls & Surveys

Crowd-sourced and informal polls, community polls, platform surveys. Useful for capturing distributions of opinion or experience, not causal evidence. Includes polls by independent researchers who regularly run methodologically thoughtful surveys (e.g. sex researchers, social scientists with public polling practices).

**Search strategy:** Search topic + "poll" or "survey" on Twitter/X, Substack, Reddit. Look for independent researchers running public surveys.

*Established institutional pollsters:* Pew Research Center (pewresearch.org), YouGov (yougov.com), General Social Survey (gss.norc.org), Understanding America Study (uasdata.usc.edu).
*Independent and community polls:* Locate independent researchers and community polls relevant to the specific question; note community skew and any obvious selection dynamics explicitly. Do not anchor to a single named independent pollster — find the active researchers in the relevant domain.

Uses the shared Explore format above (Attribution = Pollster). Includes the conditional Data and Sample fields.

---

### 5. Public Forums & Anecdotal Accounts

Reddit threads, Hacker News discussions, Quora answers, forum posts containing firsthand accounts and aggregated anecdotes. Valuable for lived experience and range of perspectives.

**Search strategy:** Per Rule 4 (Both Sections), identify which communities are likely to host firsthand accounts relevant to the question — these will differ by topic. Search Reddit (site:reddit.com + topic, or reddit.com/search), Hacker News (hn.algolia.com), Quora, and other forums. Look for threads with detailed individual accounts, not just top-voted popular opinions.

Named communities are deliberately omitted to avoid anchoring. For any question with ideological dimensions, locate communities representing distinct positions on that question and note community skew explicitly for each. Where lived experience matters, prioritize communities of the affected population over communities discussing them. Do not limit to English-language or contemporary sources where relevant alternatives exist.

Uses the shared Explore format above (Attribution = Community).

---

### 6. Videos

YouTube talks, interviews, documentaries, personal accounts. Especially useful when a transcript is available for direct quotation.

**Search strategy:** Search YouTube directly. Prioritize sources with transcripts for accuracy.

*Institutional academic sources:* University OpenCourseWare and lecture channels (MIT, Stanford, Yale, etc.). Search by professor or department + topic for academic lectures.
*Podcasts, video essayists, and documentary content:* Locate creators relevant to the specific question across ideological and disciplinary traditions per Rule 4. Prioritize content with transcripts or detailed show notes for citation accuracy. Historical documentary footage, archival interviews, and non-English-language video content are all valid where relevant.

Uses the shared Explore format above (Attribution = Creator). Includes the conditional Transcript field.

---

## Output Format

Generate a Markdown file only; do not write findings to chat. The Markdown file is the sole output.

**Concise language mandatory in every section.** Each entry, sub-bullet, and Methodology line states what is needed in the fewest words that preserve the substance. No throat-clearing, no transitional sentences, no restating the rule before applying it, no "it is worth noting that," no parenthetical asides that repeat the main clause. Methodology lines are facts, not paragraphs: "PubMed: 6 queries, 30 assessed, 6 included" is correct; a sentence describing what PubMed is or why the model chose it is not. Critique/Limitations bullets are one sentence each, not stacked clauses. The Methodology section in particular tends toward verbosity and must be audited for it before the file is written.

1. Structure the report in two sections:
   - **Empirical Research**: Empirical Studies only, ranked Excellent first then descending by quality.
   - **Explore**: All other source types (Theoretical Essays, Analytical Blogs, Polls, Forums, Videos), ranked Remarkable first then descending by Noteworthiness. Do not sub-group by source type within Explore.
2. **Random ordering within tier — SHA-256 of source URL, mandatory.** Within each Quality tier (Excellent, Good, Moderate, Weak) and within each Noteworthiness tier (Remarkable, Noteworthy, Routine), entries must be ordered by ascending SHA-256 hex digest of the canonical source URL. No other ordering method is permitted: not encounter order, not author surname, not recency, not the model's view of which finding is more compelling, not "alphabetized then sliced." The reason this is mandatory and not a default is that other methods all have correlation paths to the model's search trajectory or priors (encounter order encodes which queries the model chose to run; alphabetization correlates with author seniority and field-incumbency; recency encodes attention bias). SHA-256 of URL is the only listed method whose ordering is mechanically independent of those signals.

   Implementation: compute the hash on the exact URL used in the entry's Source field, lowercased and with no trailing slash. State the method used in Methodology as "ordered by ascending SHA-256 hex digest of canonical source URL"; do not state the digests themselves in the report. If two entries share a URL (rare; e.g., two findings from the same paper), order them by SHA-256 of the URL plus an index suffix (`{url}#1`, `{url}#2`).
3. Write the full report to `/mnt/user-data/outputs/research_report.md`
4. Present with `present_files`. Do not summarize or repeat findings in chat; only present the file.

### Markdown Format

Use standard Markdown. Document skeleton:

```markdown
# [Question as title]

## Empirical Research

[Entries using the Format block defined under "1. Empirical Studies." Ranked Excellent first, then Good, Moderate, Weak. Random order within tier.]

## Explore

[Entries from all Explore source types (Theoretical Essays, Analytical Blogs, Polls, Forums, Videos), each using the shared Explore format defined above. Ranked Remarkable first, then Noteworthy, Routine. Do not sub-group by source type within Explore. Random order within tier.]

## Methodology

This section documents what the model did to produce the report — searches run, traditions covered, choices made. The user can audit and challenge any of it.

**Research question agreed with user:** [The framed question, with population, outcome, time horizon, term operationalization, and any flagged contested premise. If the user adjusted the framing during Rule 2, note both the original framing and the adjusted version.]

**Traditions covered (per Rules 2b and 4):**
- [Tradition 1]: typically reaches [conclusion type] on questions of this kind. Sources searched: [list].
- [Tradition 2]: typically reaches [conclusion type]. Sources searched: [list].
- [Tradition 3 — must diverge in conclusion from at least one of the others]: typically reaches [conclusion type]. Sources searched: [list].
- [Additional traditions if relevant.]
- [If the user adjusted the proposed list during Rule 2b, note both the original proposal and the approved version.]

**Cultural-default audit (per Explore Rule 5, applies if any lived-experience searches were run):**
- Cultural default(s) identified for this question: [name them; if multiple defaults exist for different populations, list each separately].
- Directions searched against each default: [for each default, state which of the two divergence directions were searched and what was found].
- Cells with thin or no results: [list any (default, direction) pair where the search returned little; this is a finding, not a skipped step].
- [If the user adjusted the model's read of cultural default(s) during Rule 2b, note both the original and the approved version.]

**Searches run (per aggregator and source category):**
- [Aggregator name]: [N queries; M results assessed; K included]
- [Continue for each aggregator searched, including non-English databases. Report counts even when nothing was included; explain why nothing was included.]
- Non-English search summary: [databases searched, results count, languages encountered, count included, count excluded with reasons.]

**First-wave search disclosure (per Both-Sections Rule 5):** [State the queries used in the first wave of searching — i.e., before any results had been returned that could justify follow-up. Confirm that no author surnames, study acronyms, program names, or specific paper titles drawn from the model's prior knowledge appeared in those queries. If prior knowledge was used to identify a gap mid-research, describe how, and confirm the resulting query was still parameter-based rather than name-based.]

**Ordering method within tier:** ordered by ascending SHA-256 hex digest of canonical source URL (per Output Format Rule 2; this method is mandatory and not selectable).

**Translation-excluded sources (per Rule 4):** [Non-English sources found but excluded because the translation could not be verified to a confident standard. For each: database, author or title, language, and enough context that the user can pursue it manually. If none, write "None."]

**Known gaps:** [Anything the model could not find or could not verify. Paywalled abstracts, suspected literatures the model lacks coverage of, areas where searches returned nothing. Do not pad, but do not omit known gaps.]

```

Cross-references within Critique/Limitations use heading anchor links: [[N]](#n) — Markdown auto-generates anchors from ### headings, so ### [1] produces anchor #1.