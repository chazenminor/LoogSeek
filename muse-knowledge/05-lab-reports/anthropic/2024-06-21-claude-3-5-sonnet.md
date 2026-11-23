# Claude 3.5 Sonnet
> ID: MK-20261123-LR-005 · Version: v1.0 · Created: 2026-11-23 · Updated: 2026-11-23
> Status: Draft (deep dive, primary source read 2026-11-23)
> Official links: https://www.anthropic.com/news/claude-3-5-sonnet
> Related: MK-20261123-EM-002 (close-reading tool)

> **Date conflict — flagged, not resolved.** The Anthropic news page itself carries metadata `Published: 2024-06-21T00:00:00.000Z` (fetched 2026-11-23). The same launch is commonly cited as **2024-06-20** (e.g. secondary chronologies list 2024-06-20; the announcement went live late on 2024-06-20 US time in many accounts). This file uses **2024-06-21** in its filename per the news-page metadata, but the true publication moment is ambiguous between 2024-06-20 and 2024-06-21 depending on timezone and on when the page was posted vs. timestamped. Do not treat either date as settled without a dated primary artefact (e.g. the original social post or a dated press timestamp).
>
> **What this document is.** A close reading (tool MK-20261123-EM-002, template A–H) of Anthropic's launch announcement for Claude 3.5 Sonnet **plus** its companion technical document, the *Claude 3.5 Sonnet Model Card Addendum* (June 2024), which is the document linked from Anthropic's system-cards page as "Claude Sonnet 3.5, June 2024" and which carries all the benchmark numbers. The announcement page as fetched contains **no benchmark table and no numeric results at all** — every number in §E comes from the addendum, not from the announcement prose. This is a **model launch / capability claim**, not a peer-reviewed paper, not a training report, and not a controlled experiment. It is evaluated here as a capability claim: what Anthropic asserts, what evidence it shows, and what it does not show.
>
> **Sources actually read (2026-11-23, text fetch only — no live browser visit):**
> 1. Announcement: https://www.anthropic.com/news/claude-3-5-sonnet — full text fetched; 81 lines. Note the page is **not** byte-identical to its June 2024 original: it carries an "Update Aug 28, 2025" consumer-terms notice at the top and metadata `Updated: 2026-11-13T22:37:05.593Z`.
> 2. Addendum: https://assets.claude.com/fed9cc193a14b84131812372d8d5857f8f304c52.pdf — *Claude 3.5 Sonnet Model Card Addendum*, full text fetched (559 lines). Verified as the document linked from https://www.anthropic.com/system-cards under "Claude Sonnet 3.5, June 2024" (outlink 20 on that page resolves to this same PDF).
> 3. System-cards index: https://www.anthropic.com/system-cards — confirms a "Claude Sonnet 3.5, June 2024" entry exists.
>
> **Reading passes:** 2 full passes over the announcement + addendum (Keshav pass 1 for five Cs; pass 2 over all tables, safety section, and references). No pass 3 (virtual re-implementation) was possible: there is no method, code, prompt, or harness to re-implement. Time: single session, 2026-11-23.

---

## A. Identification

| Field | Value |
|---|---|
| Title | Introducing Claude 3.5 Sonnet (announcement); *Claude 3.5 Sonnet Model Card Addendum* (technical companion) |
| Author | Anthropic (corporate author; no individual authors named on either document) |
| Venue / type | Company blog / model announcement + model-card addendum (lab report, not peer-reviewed, no arXiv version found) |
| Date | **2024-06-21** per news-page metadata; **commonly cited as 2024-06-20** — see date-conflict flag above |
| Model version | "Claude 3.5 Sonnet" — first release of the Claude 3.5 family. No checkpoint identifier, API model ID, parameter count, or version string is reported in either document. The October 2024 addendum later distinguishes this as the "original" snapshot vs. the "upgraded" (new) Claude 3.5 Sonnet; that distinction does not exist in the June sources. |
| Companion documents | Claude 3 Model Card (March 2024, main card; §5.5 referenced for human-eval win-rate method — **not** read in this deep dive, see §H / Unverified); Responsible Scaling Policy (RSP); RSP Evaluations Report – Claude 3 Opus (May 2024, ref [23]) |
| Analyst / date | Deep dive created 2026-11-23; primary sources read 2026-11-23 |
| Report type (tool §A) | Existing-system analysis / capability announcement — **not** a measurement study with a stated research question, **not** a research prototype paper, **not** a dataset/benchmark paper |
| Peer review | No. Vendor-published; safety portion reviewed pre-deployment by the UK AI Safety Institute (UK AISI) and explored by METR, but their results are **not** published in these sources |

**One-sentence summary:** Anthropic launched a mid-tier model, Claude 3.5 Sonnet, claiming it beats its previous flagship (Claude 3 Opus) and leading competitors on most reported benchmarks while running at twice Opus's speed and at Sonnet-tier pricing — and it supports that claim with a vendor-run benchmark table, an internal agentic-coding evaluation, human-preference win rates, and an ASL-2 safety classification, while reporting no training details, no uncertainty, and no reproducibility artefacts.

---

## B. Question & claims (five Cs)

There is no stated research question. Reconstructed as a product claim, not a scientific question:

- **Reconstructed question (analyst's words):** "Does the new mid-tier model Claude 3.5 Sonnet match or exceed the previous flagship Claude 3 Opus and current competitor models on standard capability benchmarks, at lower cost and higher speed, while remaining within acceptable safety bounds?"
- **PICO-style adaptation:** System = Claude 3.5 Sonnet (June 2024 snapshot); Intervention = the new model itself (no intervention is manipulated — this is a comparison, not an experiment); Comparators = Claude 3 Opus / Sonnet / Haiku, GPT-4o, GPT-4 Turbo, Gemini 1.5 Pro, Llama 3 400B (instruct); Outcomes = benchmark scores, internal agentic-coding pass rate, human-preference win rates, refusal rates, long-context recall, ASL classification.

### Five Cs (pass 1)

- **Category:** Model announcement + model-card addendum (capability claim with evaluation tables).
- **Context:** Follows the Claude 3 family (March 2024). The framing move is tier inversion: a *mid-tier* Sonnet now beats the previous *flagship* Opus. It also launches a product feature, Artifacts, in the same post.
- **Correctness (do the assumptions look sound?):** Plausible direction, weak warrant — see §F/G. The headline claim ("outperforming competitor models and Claude 3 Opus on a wide range of evaluations") is literally true of *most* reported cells, but the table itself shows losses (MATH, MMMU vs. GPT-4o) and many empty competitor cells, so "wide range" does more work than the table does.
- **Contributions (claimed vs. supported):** Claimed — a new frontier-at-mid-tier model, state-of-the-art vision, a new collaborative interface (Artifacts), ASL-2 safety. Supported by the documents — the benchmark table and agentic-coding result support "better than Opus on reported benchmarks"; Artifacts is a product launch, not an evaluated claim; ASL-2 is asserted with qualitative, not quantitative, safety evidence.
- **Clarity:** 3/5. The announcement is crisp marketing prose; the addendum's tables are legible but its PDF text extraction scrambles column alignment (reconstructed in §E with care — see the note there), protocols differ across models within the same row, and key methods (human eval, safety thresholds, speed measurement) are deferred or omitted.

### Numbered claims

| # | Claim (close paraphrase, with source location) |
|---|---|
| C1 | Claude 3.5 Sonnet "raises the industry bar for intelligence, outperforming competitor models and Claude 3 Opus on a wide range of evaluations, with the speed and cost of our mid-tier model, Claude 3 Sonnet." (Announcement L6–7) |
| C2 | It "sets new industry benchmarks for graduate-level reasoning (GPQA), undergraduate-level knowledge (MMLU), and coding proficiency (HumanEval)." (Announcement L15–16; Addendum §2, Tables 1) |
| C3 | "Claude 3.5 Sonnet operates at twice the speed of Claude 3 Opus." (Announcement L19–20) |
| C4 | On the internal agentic coding evaluation it "solved 64% of problems, outperforming Claude 3 Opus which solved 38%." (Announcement L22–23; Addendum §2, Table 3) |
| C5 | "Our strongest vision model yet, surpassing Claude 3 Opus on standard vision benchmarks" — incl. state-of-the-art MathVista, ChartQA, DocVQA, AI2D. (Announcement L30–32; Addendum §2, Table 2) |
| C6 | Artifacts is a new way to use Claude — a dynamic workspace where generated content can be seen, edited, and built upon in real time. (Announcement L36–43) — a product claim, not an evaluated capability claim. |
| C7 | Safety: "our red teaming assessments have concluded that Claude 3.5 Sonnet remains at ASL-2" (Announcement L47–48); "does not pose risk of catastrophic harm" (Addendum §3.1). Pre-deployment testing by UK AISI, results shared with US AISI. |
| C8 | Pricing/access: $3 per million input tokens, $15 per million output tokens, 200K-token context window; available on Claude.ai, iOS app, API, Amazon Bedrock, Google Cloud Vertex AI. (Announcement L9–11) |
| C9 | Forward-looking: Claude 3.5 Haiku and Claude 3.5 Opus "later this year"; aim to "substantially improve the tradeoff curve between intelligence, speed, and cost every few months." (Announcement L64–65) — a roadmap statement, not a finding. |

**If C1–C2 were wrong, what would change:** the launch's central narrative — that the mid-tier had become the frontier — collapses, and with it the pricing/speed justification for deploying Sonnet over Opus or competitors. C4 is the load-bearing *practical* claim: if the agentic-coding gap is a scaffold artefact rather than a model difference, the "works with code, not just chats" story weakens even if benchmark cells hold.

---

## C. Assumptions map (first principles)

| # | Assumption (paraphrase + location) | Type | Load-bearing? | Evidence in source | What would break it | Depends on |
|---|---|---|---|---|---|---|
| A1 | Higher scores on GPQA / MMLU / HumanEval / etc. = higher general intelligence / capability ("raises the industry bar for intelligence"). Announcement L6–7, L15. | Unvalidated | **Yes — root** | None offered; the inference is asserted, not argued. | Showing the gains are benchmark-specific (memorisation, format fit, prompt fit) with no transfer to held-out real tasks. | A2, A3 |
| A2 | The reported benchmarks are uncontaminated: the model did not train on (near-)copies of the test items. | Unvalidated | **Yes** | Not reported / can't tell — no decontamination statement anywhere in either document. | Overlap analysis showing test items (or paraphrases) in training data; or a sharp drop on a fresh, post-cutoff benchmark. | — (root) |
| A3 | Cross-model cells in Tables 1–2 are comparable, i.e. differences reflect models, not evaluation protocol. | Unvalidated | **Yes** | Undermined by the tables themselves: protocols differ *within* rows (e.g. MATH: 0-shot CoT for Claude/GPT-4o vs. 4-shot for Gemini vs. 4-shot CoT for Llama 3; MGSM: 0-shot CoT vs. 8-shot for Gemini; DROP: "Variable shots" for Gemini). Competitor numbers are taken from vendors' own reports (refs [8], [9], [16], [17]), not re-run under one harness. | Re-running all models under one harness with identical prompts/shots and seeing the ranking change. | A4 |
| A4 | Competitor numbers quoted from OpenAI / Google / Meta publications are accurate and refer to the same model versions a user would meet. | Unvalidated | Yes | Citations only (Addendum refs [8], [9], [16]); no verification, no version pinning beyond "GPT-4o", "Gemini 1.5 Pro", "Llama 3 400B (April 15, 2024 checkpoint)". | Vendor re-measurement or independent replication showing different competitor scores under the stated protocol. | — |
| A5 | The internal agentic coding evaluation measures real-world software-engineering ability. Addendum §2, "Agentic Coding". | Unvalidated | Yes (for C4) | Design described in prose (real PRs, hidden tests, sandboxed, agentic loop, multi-file edits) but **n not reported**, problem selection not described, no difficulty distribution, no baseline scaffold comparison. | A public replication (e.g. SWE-bench Verified on the same snapshot) giving a very different pass rate, or showing the gap vs. Opus vanishes with a fixed scaffold. | A6 |
| A6 | The agentic scaffold / tooling given to each model in the internal evaluation was equivalent, so the 64% vs. 38% gap is a model gap. | Unvalidated | Yes (for C4) | Not reported / can't tell — scaffold, tools, iteration budget, and prompts are not described. | Same models, same scaffold, different tool budget → gap shrinks or reverses. | — |
| A7 | Human-preference win rates vs. Claude 3 Opus measure capability improvements (not style, length, or rater expectation). Addendum §2.1. | Unvalidated | Partial | Method deferred to Claude 3 main model card §5.5 (not in this document); rater n, task n, blinding, tie handling, instructions — **not reported**. | Blinded replication with length-controlled outputs showing win rates near 50%. | — |
| A8 | "Twice the speed of Claude 3 Opus" is a meaningful, reproducible speed comparison. Announcement L19. | Unvalidated | Partial (for C3) | No latency numbers, no task, no hardware, no tokens/sec, no measurement method — **not reported at all**. | Any latency measurement under stated conditions showing a materially different ratio. | — |
| A9 | Refusal-rate differences (Table 4) measure better safety calibration rather than different refusal *style* or threshold tuning. | Soft | No | Wildchat/XSTest named; sizes and sampling not reported. | Showing the same models with a different system prompt reorder the refusal ranking. | — |
| A10 | Needle-in-a-Haystack recall measures long-context retrieval ability relevant to real 200K-token use. Addendum §2, Table 5. | Soft | No | Anthropic's own variant of the task (ref [21]); synthetic needle, single fact. | Real long-document QA at 200K showing recall collapsing while NIAH stays ~99.7%. | — |
| A11 | Safety: capability gains do not cross ASL-3 because the model "does not trigger the 4x effective compute threshold" for the full RSP protocol, and targeted testing found no threshold breach. Addendum §3.1. | Soft (policy) | Yes (for C7) | Threshold values **not reported**; quantitative CBRN/cyber/autonomy results **not reported** (only "did not exceed our safety thresholds"); UK AISI results **not published here**. The 4x-compute trigger is a policy convention, not a physical fact. | Publication of the threshold values and scores; an external evaluation finding a breach; or a showing that effective-compute accounting understates the capability jump. | A12 |
| A12 | HHH (Helpful, Honest, Harmless) refusals only *underestimate* capability, and Anthropic's internal "non-refusal" elicitation recovers the true capability without inflating it. Addendum §3.2, final paragraph. | Unvalidated | Partial | Asserted; the elicitation technique is not described, and the authors themselves note results "may underestimate" a Helpful-only model. Direction of bias is assumed, not shown. | Showing the elicitation overshoots (e.g. by coaching the model into the answer) or undershoots unevenly across models. | — |
| A13 | The June 2024 snapshot evaluated in the addendum is the same model users received at launch. | Unvalidated | Yes | No checkpoint/version identifier is reported anywhere, so identity between evaluated artefact and deployed artefact cannot be checked from these sources. | API/model-ID records showing the evaluated build differs from the launch build. | — (root) |
| A14 | "State-of-the-art" means best on the reported benchmarks *as evaluated here*, not best on all relevant evaluations. | Soft (definitional) | Partial | The tables themselves contradict the unqualified reading: GPT-4o beats 3.5 Sonnet on MATH (76.6% vs. 71.1%) and MMMU (69.1% vs. 68.3%) in the same tables. | Any unreported benchmark where a competitor leads — already true within the document. | A3 |

**Root assumptions:** A1 (benchmark score = intelligence), A2 (no contamination), A13 (evaluated model = shipped model). If A2 fails, every Table 1–2 cell is suspect; if A13 fails, the numbers describe a different artefact than the product; if A1 fails, the numbers can be exactly right and the headline still be wrong.

---

## D. Design, controls, metrics, statistics

**Design type:** None of the standard experimental designs apply. This is a **vendor benchmark report**: a set of pre-existing public benchmarks run (by Anthropic, for its own model; quoted from vendor publications for competitors) plus one internal, non-public evaluation and one human-preference study. There is no hypothesis, no randomisation, no pre-registration, no control group in the experimental sense — the "controls" are the comparator models in the tables.

**What was actually done (as described):**

- **Standard benchmarks (Addendum Tables 1–2):** GPQA Diamond, MMLU (two protocols), MATH, HumanEval, MGSM, DROP, BIG-Bench Hard, GSM8K, MMMU (validation), MathVista (testmini), AI2D (test), ChartQA (test), DocVQA (test). Shot counts and chain-of-thought use are stated per row (see §E) but **prompts, harness, harness version, decoding settings (temperature, top-p, max tokens), and number of runs are not reported**.
- **Agentic coding (Table 3):** problems based on real open-source pull requests; model must implement the change (typically 3–4 files, up to 20); hidden tests must all pass; model may write/run code in an agentic loop and self-correct; sandboxed, no internet. **n (number of problems), problem selection criteria, time/step budget, scaffold, and prompts are not reported.**
- **Refusals (Table 4):** Wildchat (toxic / non-toxic splits) and XSTest; correct vs. incorrect refusal rates. **Dataset sizes, sampling, and prompt format not reported.**
- **Needle in a Haystack (Table 5):** Anthropic's own variant of the task; average recall at context lengths up to 200K. **Needle type, prompt (one "modified prompt" mentioned for Figure 1), number of trials per length — not reported.**
- **Human feedback (§2.1, Figure 3):** raters chat with models on task-specific instructions; win rate vs. Claude 3 Opus baseline (Opus = 50% by construction). Method deferred to Claude 3 main model card §5.5. **Rater count, task count, blinding, agreement, tie handling, significance — not reported in this document.**
- **Safety (§3):** CBRN automated knowledge tests + non-expert uplift; cybersecurity capture-the-flag; autonomy via code-writing evals; "thresholds of concern" defined per domain (values not reported); UK AISI pre-deployment testing of a "near-final model"; METR "initial exploration" of autonomy. **No quantitative safety results are reported.**

**Controls / baselines:** Claude 3 Opus, Claude 3 Sonnet, Claude 3 Haiku (and Claude 2.1 for NIAH only) as internal baselines; GPT-4o, GPT-4 Turbo, Gemini 1.5 Pro, Llama 3 400B as external comparators. **Tuning/compute matching: Can't tell** — and in fact demonstrably *unmatched* in protocol: different shot counts and CoT settings across models within the same benchmark row (see A3/A4). Competitor scores were not re-run by Anthropic; they are quoted from the competitors' own publications. This is the single most important design fact in the document.

**Blinding:** Not reported / can't tell for human evals (and the method is deferred to another document). Not applicable to automated benchmarks, except that benchmark contamination status is unreported.

**Metrics:** Accuracy / pass rate (%, one decimal), F1 (DROP), ANLS (DocVQA), relaxed accuracy (ChartQA), average recall (NIAH), win rate (human eval), refusal rate (Table 4). No primary vs. secondary metric distinction is made — every reported number is presented as supporting the headline.

**Statistics — everything below is "not reported":**

| Item | Status |
|---|---|
| Seeds / number of runs | Not reported (all tables) |
| Error bars (SD / SE / CI) | Not reported for any benchmark table (Tables 1–5). The October addendum's OSWorld table does report 95% CIs — the June addendum does not |
| Effect sizes, p-values, multiple-comparison correction | Not reported |
| Sample sizes (n) | Not reported for any benchmark (GPQA Diamond, MATH, HumanEval, agentic coding, human eval, Wildchat/XSTest, NIAH trials) |
| Compute (training FLOPs, hardware, time) | Not reported |
| Training data, architecture, parameters | Not reported |
| Eval harness + version, prompts, decoding | Not reported (except shot/CoT labels per row) |
| Contamination / decontamination analysis | Not reported |
| Data / code availability | No code, no prompts, no raw results, no eval scripts released with these documents |

**Checklist verdict (NeurIPS / Pineau items, applied to this document as a capability claim):** claims↔results consistency — *partial* (headline overstates the mixed table, see §G); limitations — *partially stated* (see §H, author-stated); reproducibility path — **No**; experimental setting — *partial* (shots/CoT only); statistical significance — **No**; compute — **No**; data/code — **No**; ethics/safety — *partial* (ASL-2 asserted, quantitative results withheld).

**Plain statement (required):** this is a model launch post plus a vendor model card, not a peer-reviewed paper. There is no experiment in the scientific sense — no manipulated variable, no randomisation, no statistical inference. The correct standard of appraisal is "how much should a reader trust these vendor-reported numbers and the claims built on them", not "was the hypothesis test well powered".

---

## E. Numbers ledger

**How to read this ledger.** Every number below is a *vendor-reported point estimate* from the June 2024 addendum (or, where marked, the announcement). Conditions are given exactly as the source states them; where the source is silent, the cell says **not reported** rather than being filled in from outside knowledge. **A number without conditions is unusable as evidence** — and for this document, *no* number carries a complete condition set (no harness, no decoding, no n, no seeds, no error bars anywhere). Competitor cells are quoted from competitors' own publications, not measured by Anthropic.

**Reconstruction note on Table 1:** the PDF text extraction scrambles the table's column order. The reconstruction below follows the addendum's stated column order — Claude 3.5 Sonnet | Claude 3 Opus | Claude 3 Sonnet | GPT-4o | GPT-4 Turbo | Gemini 1.5 Pro | Llama 3 400B (Instruct) — and cross-checks each row's values against the row's stated protocols and against the October addendum's re-presentation of the June numbers (used only as a consistency check on column assignment, not as a source of new numbers). Where the extraction left genuine ambiguity it is flagged in the row.

### E1. Addendum Table 1 — reasoning, coding, QA (Claude 3.5 Sonnet column; comparators in parentheses)

| # | Number | What (metric, conditions as stated) | Exact conditions (model / checkpoint / split / harness / decoding / n / seeds) | Location | Same-condition baseline / comparator | Supports claim? |
|---|---|---|---|---|---|---|
| E1.1 | **59.4%** | GPQA (Diamond), graduate-level Q&A, 0-shot CoT | Model: Claude 3.5 Sonnet (June 2024 snapshot; checkpoint not identified). Split/version: GPQA Diamond (ref [1]); version not stated. Harness: not reported. Decoding: not reported. n: not reported. Seeds: not reported | Add. Table 1 | Opus 50.4%; Sonnet 40.4%; GPT-4o 53.6%; GPT-4T 48.0%; Gemini —; Llama 3 400B — | Yes — leads all reported cells |
| E1.2 | **67.2%** | GPQA (Diamond), Maj@32, 5-shot CoT | As E1.1, except majority vote over 32 samples (Maj@32) | Add. Table 1 | Opus 59.5%; Sonnet 46.3%; all others — | Yes — leads reported cells; **note:** no competitor ran this protocol, so "industry benchmark" here means "best among Claude models" |
| E1.3 | **90.4%** | MMLU, general reasoning, 5-shot CoT | As above; MMLU (ref [2]); split/version not stated | Add. Table 1 | Opus 88.2%; Sonnet 81.5%; GPT-4o —; GPT-4T —; Gemini —; Llama — | Partial — leads Claude models only; **no competitor value is reported under this protocol**, so the announcement's "new industry benchmark for MMLU" rests on a protocol no competitor was measured on |
| E1.4 | **88.7%** | MMLU, 5-shot (no CoT) | As above | Add. Table 1 | Opus 86.8%; Sonnet 78.3%; GPT-4o —; GPT-4T —; Gemini 85.9%; Llama 86.1% | Partial — leads reported cells, but GPT-4o cell is empty |
| E1.5 | **88.3%** | MMLU, 0-shot CoT | As above. Footnote 2: OpenAI's simple-evals suite lists 88.7% for GPT-4o and 86.5% for GPT-4-turbo-2024-04-09 under 0-shot CoT | Add. Table 1, fn 2 | Opus 85.7%; Sonnet 77.1%; **GPT-4o 88.7%**; GPT-4T 86.5%; Gemini —; Llama — | **No — GPT-4o scores higher (88.7% vs. 88.3%) under the identical 0-shot CoT protocol.** This directly qualifies C2 |
| E1.6 | **71.1%** | MATH, mathematical problem solving, 0-shot CoT | MATH (ref [10]); split/version not stated | Add. Table 1 | Opus 60.1%; Sonnet 43.1%; **GPT-4o 76.6% (0-shot CoT)**; GPT-4T 72.6% (0-shot CoT); Gemini 67.7% (**4-shot, different protocol**); Llama 57.8% (**4-shot CoT, different protocol**) | **No — loses to GPT-4o by 5.5 pp and to GPT-4T by 1.5 pp.** Also: Gemini/Llama were measured under different protocols, so those cells are not comparable |
| E1.7 | **92.0%** | HumanEval, Python coding tasks, 0-shot | HumanEval (ref [3]); pass@1 implied, not stated | Add. Table 1 | Opus 84.9%; Sonnet 73.0%; GPT-4o 90.2%; GPT-4T 87.1%; Gemini 84.1%; Llama 84.1% | Yes — leads all reported cells, by 1.8 pp over GPT-4o |
| E1.8 | **91.6%** | MGSM, multilingual math, 0-shot CoT | MGSM (ref [11]) | Add. Table 1 | Opus 90.7%; Sonnet 83.5%; GPT-4o 90.5%; GPT-4T 88.5%; Gemini 87.5% (**8-shot, different protocol**); Llama — | Yes — leads reported cells, by 0.9 pp over Opus; Gemini cell not comparable (8-shot) |
| E1.9 | **87.1** | DROP, reading comprehension + arithmetic, F1 score, 3-shot | DROP (ref [12]). Footnote 1: DROP evaluated on the **pretrained** version of the model, not the Instruct version (unlike the rest of the table) | Add. Table 1, fn 1 | Opus 83.1; Sonnet 78.9; GPT-4o 83.4; GPT-4T 86.0; Gemini 74.9 ("Variable shots"); Llama 83.5 | Yes — leads, by 1.1 over GPT-4T. **Caveat:** pretrained-vs-instruct mixing (fn 1) and Gemini's "Variable shots" make this row the least standardised in the table |
| E1.10 | **93.1%** | BIG-Bench Hard, mixed evaluations, 3-shot CoT | BBH (refs [13, 14]). Footnote 1: evaluated on the **pretrained** version | Add. Table 1, fn 1 | Opus 86.8%; Sonnet 82.9%; GPT-4o —; GPT-4T —; Gemini 89.2%; Llama 85.3% | Yes — leads reported cells; no GPT-4o/T values reported |
| E1.11 | **96.4%** | GSM8K, grade-school math, 0-shot CoT | GSM8K (ref [15]) | Add. Table 1 | Opus 95.0%; Sonnet 92.3%; GPT-4o —; GPT-4T —; Gemini 90.8% (**11-shot**); Llama 94.1% (**8-shot CoT**) | Partial — leads reported cells, but both non-Claude competitors used different shot counts and GPT cells are empty |

### E2. Addendum Table 2 — vision / multimodal (all 0-shot; MMMU, MathVista, ChartQA use CoT before the final answer)

| # | Number | What (conditions as stated) | Exact conditions | Location | Comparators | Supports claim? |
|---|---|---|---|---|---|---|
| E2.1 | **68.3%** | MMMU (validation), visual QA, 0-shot + CoT | MMMU validation split (ref [18]); harness/decoding/n/seeds: not reported | Add. Table 2 | Opus 59.4%; Sonnet 53.1%; **GPT-4o 69.1%**; GPT-4T 63.1%; Gemini 62.2% | **No — GPT-4o leads by 0.8 pp.** C5's "surpassing … on standard vision benchmarks" does not hold for MMMU |
| E2.2 | **67.7%** | MathVista (testmini), math reasoning, 0-shot + CoT | MathVista testmini (ref [4]) | Add. Table 2 | Opus 50.5%; Sonnet 47.9%; GPT-4o 63.8%; GPT-4T 58.1%; Gemini 63.9% | Yes — leads by 3.8 pp over Gemini |
| E2.3 | **94.7%** | AI2D (test), science diagrams, 0-shot | AI2D test (ref [7]) | Add. Table 2 | Opus 88.1%; Sonnet 88.7%; GPT-4o 94.2%; GPT-4T 89.4%; Gemini 94.4% | Yes — leads, by 0.3 pp over Gemini and 0.5 pp over GPT-4o (within any plausible noise band — but no error bars are reported, so this cannot be assessed) |
| E2.4 | **90.8%** | ChartQA (test, relaxed accuracy), 0-shot + CoT | ChartQA test (ref [5]) | Add. Table 2 | Opus 80.8%; Sonnet 81.1%; GPT-4o 85.7%; GPT-4T 78.1%; Gemini 87.2% | Yes — leads by 3.6 pp over Gemini |
| E2.5 | **95.2%** | DocVQA (test, ANLS score), 0-shot | DocVQA test (ref [6]) | Add. Table 2 | Opus 89.3%; Sonnet 89.5%; GPT-4o 92.8%; GPT-4T 87.2%; Gemini 93.1% | Yes — leads by 2.1 pp over Gemini |

### E3. Agentic coding, refusals, long context, human preference (Addendum Tables 3–5, §2.1)

| # | Number | What (conditions as stated) | Exact conditions | Location | Comparators | Supports claim? |
|---|---|---|---|---|---|---|
| E3.1 | **64%** | Internal agentic coding eval: % of problems where the final submission passed **all** tests | Real open-source PRs; multi-file edits (typically 3–4 files, up to 20); agentic loop with code execution + self-correction; sandboxed, no internet; hidden tests. **n, problem selection, step/time budget, scaffold, prompts, seeds: not reported** | Add. §2 + Table 3 | Opus 38%; Sonnet 21%; Haiku 17%. **No external model was run on this eval** | Yes, within Claude models — a 26 pp gap over Opus. But it is an internal, non-public eval with no n and no scaffold description, so the number cannot be independently checked or compared with any other lab's model |
| E3.2 | **96.4%** | Wildchat Toxic: correct refusals (higher better) | Wildchat (ref [19]); sizes/sampling/prompts: not reported | Add. Table 4 | Opus 92.0%; Sonnet 93.6%; Haiku 90.0% | Yes — highest correct-refusal rate among Claude models |
| E3.3 | **11.0%** | Wildchat Non-toxic: incorrect refusals (lower better) | As above | Add. Table 4 | Opus 11.9%; Sonnet 8.8%; Haiku 6.6% | Partial — better than Opus, but **worse than Sonnet (8.8%) and Haiku (6.6%)**; the addendum's prose ("outperformed Opus on both dimensions") is true only vs. Opus, not vs. all Claude models |
| E3.4 | **1.7%** | XSTest: incorrect refusals (lower better) | XSTest (ref [20]); size: not reported | Add. Table 4 | Opus 8.3%; Sonnet 36.6%; Haiku 33.1% | Yes — large improvement over all Claude models; the most striking refusal result in the document |
| E3.5 | **99.7%** | Needle in a Haystack: average recall, all context lengths (up to 200K) | Anthropic's own variant (refs [21, 22]); needle/prompt/trials-per-length: not reported | Add. Table 5, Fig. 1–2 | Opus 99.4%; Sonnet 95.4%; Haiku 95.9%; Claude 2.1 94.5% | Yes — but near-ceiling for both 3.5 Sonnet and Opus; the task is a synthetic single-fact retrieval, not a test of real long-document reasoning |
| E3.6 | **99.7%** | Needle in a Haystack: average recall at 200K context length specifically | As above | Add. Table 5 | Opus 98.3%; Sonnet 91.4%; Haiku 91.9%; Claude 2.1 92.7% | Yes — holds recall at the full 200K window where other models drop ~7 pp |
| E3.7 | **82% / 73% / 73%** | Human-preference win rate vs. Claude 3 Opus baseline, domain experts: Law 82%, Finance 73%, Philosophy 73% | Raters chat with models, task-specific instructions; win-rate method deferred to Claude 3 main card §5.5. **Rater n, task n, blinding, ties: not reported** | Add. §2.1 (prose; Fig. 3) | Opus = 50% by construction (baseline vs. itself) | Yes, as reported — but with no n and no method in this document, the win rates are uninterpretable as effect sizes |
| E3.8 | (other Fig. 3 per-task win rates) | Human-preference win rates for Coding, Documents, Medicine, Creative Writing, Multilingual, STEM, Instruction-Following, Honesty, Vision, Harmlessness, Helpful-Only | Figure 3 only; the PDF text extraction scrambles the label-to-value mapping | Add. Fig. 3 | Opus = 50% by construction | **UNVERIFIED — see "Unverified / could not verify".** Only the three values in E3.7 are confirmed by the prose; the rest are not reliably recoverable from the text fetch and are not presented as established |

### E4. Announcement-only claims (no table behind them)

| # | Number / claim | What | Conditions | Location | Verdict |
|---|---|---|---|---|---|
| E4.1 | **2× the speed of Claude 3 Opus** | Speed ratio | **No conditions at all:** no latency, throughput, task, hardware, or measurement method reported in either document | Announcement L19–20 | **Unverifiable as stated.** A ratio with no numerator, denominator, or measurement is not a usable number |
| E4.2 | **$3 / $15 per million input / output tokens; 200K-token context window** | Price + context | Launch pricing and context as stated; availability: Claude.ai, iOS app, API, Bedrock, Vertex AI | Announcement L9–11 | Verified as a *stated* launch fact (not a measured result). Prices are June 2024 launch prices, not current prices |
| E4.3 | **ASL-2** | Safety classification | Based on targeted (not full-RSP) testing; quantitative results not reported; UK AISI tested a "near-final" model | Announcement L47–48; Add. §3 | Verified as a *stated classification*; the evidence behind it is not shown in these sources |

**Ledger summary (counting only cells with a stated comparator):** of the 11 Table 1 rows, 3.5 Sonnet leads all reported cells in 7, leads only Claude models or an incomplete field in 3, and loses outright in 2 (MMLU 0-shot CoT, MATH — E1.5, E1.6; GPQA Maj@32 and MMLU 5-shot CoT have no competitor cells at all). In Table 2 it leads 4 of 5 and loses MMMU. The honest summary of the document's own tables is "leads on most reported benchmarks, loses on MATH and MMMU, and several headline comparisons have no competitor data" — not the announcement's unqualified "outperforming competitor models … on a wide range of evaluations".

---

## F. Argument map (Argdown-style)

```
[Claim C1: 3.5 Sonnet raises the industry bar; beats Opus + competitors at Sonnet speed/cost]
  + <A1: Table 1 — leads Opus on every row; leads competitors on most reported cells>
      + [E1.1 GPQA 59.4% > GPT-4o 53.6%] [E1.7 HumanEval 92.0% > GPT-4o 90.2%]
      + [E2.2–E2.5 vision leads]
      - [E1.6 MATH 71.1% < GPT-4o 76.6%] [E2.1 MMMU 68.3% < GPT-4o 69.1%] [E1.5 MMLU 0-shot CoT 88.3% < GPT-4o 88.7%]
      - (co-premise A3 fails in part: protocols differ within rows; competitor cells quoted, not re-run; several cells empty "—")
  + <A2: Table 3 — agentic coding 64% vs Opus 38%>
      - (co-premise A5/A6: internal, non-public eval; n, scaffold, budget unreported; no external model run on it)
  + <A3: Human win rates — experts prefer 3.5 Sonnet (Law 82%, Finance 73%, Philosophy 73%)>
      - (co-premise A7: method deferred to another document; n/blinding/ties unreported)
  + <A4: Announcement — 2x Opus speed at Sonnet price ($3/$15, 200K context)>
      - (co-premise A8: no speed measurement reported at all; price/context are stated facts, not measurements)
  - [Objection O1: "new industry benchmark for MMLU" (C2) — the 90.4% figure is 5-shot CoT, a protocol with no competitor cell; under the shared 0-shot CoT protocol GPT-4o wins]
  - [Objection O2: no uncertainty anywhere — with n unreported and no error bars, gaps of 0.3–1.8 pp (AI2D, HumanEval, MGSM, MMMU) cannot be distinguished from noise]
  - [Objection O3: contamination unaddressed — no decontamination statement; benchmark gains could reflect training-data overlap]
  + [Rebuttal offered by the document to O1–O3: none. The document does not acknowledge any of these objections.]
  + [Rebuttal offered to safety concerns (C7): targeted CBRN/cyber/autonomy testing + UK AISI pre-deployment testing + ASL-2 classification]
      - (but: thresholds and quantitative results withheld; UK AISI results not published here; "near-final" model tested)

[Claim C7: ASL-2, no catastrophic-harm risk]
  + <B1: did not trigger the 4x effective-compute threshold for full RSP protocol>
      - (co-premise A11: threshold is a policy convention; its value and the model's effective compute are unreported)
  + <B2: targeted testing found no threshold-of-concern breach>
      - (threshold values and scores unreported — the reader cannot check the claim)
  + <B3: external testing by UK AISI + METR exploration>
      - (results not published in these sources; UK AISI tested a "near-final" model, not necessarily the shipped one)
  + [Author's own caveat, Add. §3.2: HHH refusals may cause capability evals to underestimate the model; internal elicitation used to compensate — technique undescribed]
```

**Weakest link (named):** **A3 — the comparability of the cross-vendor table (Table 1, and by extension Table 2).** It is maximally load-bearing (C1 and C2 both rest on it) and has the weakest support: protocols are asymmetric *within* rows, competitor numbers are second-hand quotations from vendor publications rather than re-measurements, cells are missing exactly where a competitor might win (MMLU 5-shot CoT, GPQA Maj@32), and there is no uncertainty quantification to say whether any gap is real. The second-weakest link is A5/A6 (the internal agentic-coding eval): the largest single claimed gap (64% vs. 38%) comes from the least transparent measurement in the document. Evidence that would strengthen the weakest link: a single-harness re-run of all seven models with identical prompts, shots, and decoding, with n, seeds, and confidence intervals reported; evidence that would break it: the same re-run reordering the leaderboard on MATH/MMLU/MMMU or shrinking the GPQA/HumanEval gaps inside noise.

---

## G. Validity judgements

Scores are 1–5 (5 = strong). The object being judged is the *announcement + addendum as evidence for C1–C7*, not the model itself — the model may be better or worse than the document shows; the document does not let a reader tell.

| Dimension | Score | Reasons |
|---|---|---|
| Internal validity | **2/5** | Threats, named: (1) **protocol asymmetry** — different shots/CoT per model within rows (MATH, MGSM, DROP, GSM8K); (2) **second-hand competitor data** — GPT-4o/Gemini/Llama numbers quoted from vendor publications, not measured under Anthropic's harness; (3) **no contamination analysis** — leakage would inflate exactly the reported cells; (4) **internal eval opacity** — agentic coding has no n, no scaffold, no selection criteria; (5) **human-eval method deferred** to another document. What helps: within-Claude comparisons were presumably run under one harness, and the direction of the Opus gap is consistent across many independent benchmarks, which is harder to fake by protocol choice alone. |
| External validity | **2/5** | Generalises to: standard academic benchmarks of the kind reported, and to short, single-turn QA/coding tasks similar to them. Does **not** generalise to: multi-turn real-world work (only the opaque internal eval speaks to this, and it cannot be checked), long-document reasoning at 200K (NIAH is a synthetic single-fact retrieval), non-English use beyond MGSM, tool use, or deployment conditions (latency, cost at scale, failure modes). No user study in the wild; Artifacts is announced, not evaluated. |
| Construct validity | **2/5** | The document treats "benchmark accuracy" as "intelligence" (A1) without argument. GPQA/MMLU measure multiple-choice test-taking under a specific prompt format as much as they measure knowledge; HumanEval measures short-function synthesis; NIAH measures synthetic retrieval. The agentic-coding eval is the closest to a real-world construct and is the least transparent. The refusal metrics (Table 4) are the most construct-clean part of the document — they measure what they claim (refusal behaviour on named datasets) — but even there, dataset sizes are unreported. |
| Statistical-conclusion validity | **1/5** | No n, no seeds, no runs, no error bars, no CIs, no p-values, no effect sizes, no multiple-comparison correction — for any number in the document. Several headline gaps (AI2D +0.3 pp, MGSM +0.9 pp, MMMU −0.8 pp, HumanEval +1.8 pp) are of a size that could plausibly be noise on benchmarks of these sizes, and the reader is given no way to tell. The October addendum's later OSWorld table *does* report 95% CIs, which only underlines their absence here. |
| Reproducibility (re-run the headline results) | **1/5** | A capable reader cannot re-run any headline number from these documents: no prompts, no harness, no harness version, no decoding settings, no seeds, no code, no data for the internal evals, no checkpoint identifier for the model. The public benchmarks could in principle be re-run against the API, but the *exact* June 2024 snapshot is not pinned (the API model has since been superseded), so even that route cannot reproduce the June numbers. The safety results are entirely non-reproducible (thresholds and scores withheld). |
| **Parsimony check** | — | Paper's explanation: "the new model is more capable across the board (better reasoning, coding, vision)". Simplest alternative explaining the same data: "the new model is better on these benchmarks under these protocols" — which is the same data with fewer assumptions, since it drops the leap from benchmark scores to general intelligence (A1) and from an internal eval to real-world engineering ability (A5). Occam does not decide between them on the *numbers* (both fit the reported cells), but the narrower explanation assumes strictly less, so the broader headline claim carries an unjustified surplus. Where the data differ from the explanation: MATH and MMMU losses, and the missing competitor cells, are *not* explained by "more capable across the board" without additional assumptions (e.g. "those benchmarks don't measure what matters") — assumptions the document never states. |

**Razors applied:**

- **Hitchens:** the following are asserted without evidence and are marked "to be dismissed until evidence is offered": 2× Opus speed (no measurement); "marked improvement in grasping nuance, humor, and complex instructions" (no measurement — announcement L15–16); "exceptional at writing high-quality content with a natural, relatable tone" (no measurement); "state-of-the-art" as an unqualified claim (contradicted in-table on MATH/MMMU); Artifacts as a "dynamic workspace" improvement (product description, no user evaluation).
- **Alder (unfalsifiable / untestable as stated):** "raises the industry bar for intelligence" — "intelligence" is never operationalised, so the headline cannot be tested; testable only in its reduced form ("scores higher on benchmarks X, Y, Z under protocol P"). "Grasping nuance and humor" — same problem.
- **Hume (causal sufficiency):** the document never identifies a *cause* of the improvement (no training change is described at all — no data, compute, architecture, or method), so there is no causal claim to check; the mechanism behind the gain is entirely unexamined. A 26 pp agentic-coding gap with no stated mechanism and no scaffold description is a large effect with no offered cause.
- **Popper:** the benchmark claims are falsifiable in principle (re-run the benchmarks) but not in practice from these documents (no harness/prompts/snapshot pin). The safety claim (ASL-2) is effectively unfalsifiable by an outside reader because thresholds and scores are withheld.
- **Sagan:** the strongest claim — a mid-tier model dethroning the previous flagship and leading the industry — is a substantial one; the evidence offered (a single vendor-run table with no uncertainty) does not meet the bar that claim sets for itself.

**Overall judgement:** The document establishes, to a moderate degree, that the June 2024 Claude 3.5 Sonnet snapshot scored higher than Claude 3 Opus on the benchmarks Anthropic chose to report, under the protocols Anthropic chose — the within-family gap is large, consistent across many benchmarks, and unlikely to be entirely artefactual. It does **not** establish the headline ("outperforming competitor models … sets new industry benchmarks") in its unqualified form: the tables themselves show losses and empty cells, the cross-vendor comparison is methodologically asymmetric and second-hand, and no number carries any uncertainty. The safety case is an assertion backed by undisclosed thresholds and unpublished external results. The agentic-coding result (64% vs. 38%) is the most practically interesting number and the least checkable.

**Bottom line in one sentence:** A credible within-family improvement claim wrapped in an overstated industry-leadership claim, supported by a vendor table that is useful as a record of what Anthropic reported in June 2024 and inadequate as evidence for the generality of what it claims.

**Confidence: medium** — medium, not low, because the within-Claude gaps are large and consistent; not high, because the cross-vendor comparison, the statistics, and the safety evidence are all unverifiable from the sources.

---

## H. Falsifier, alternative explanations, limitations, what to watch

### Falsifiers (one per main claim; stated as observable outcomes)

- **C1/C2 (beats competitors / new industry benchmarks):** an independent single-harness re-run (identical prompts, shots, decoding, model versions pinned) in which GPT-4o matches or beats 3.5 Sonnet on GPQA Diamond and HumanEval, or in which the MMLU 5-shot CoT gap vs. GPT-4o disappears. *Already partially realised within the document itself:* GPT-4o leads on MATH and MMMU and on MMLU 0-shot CoT.
- **C3 (2× Opus speed):** a published latency/throughput measurement under stated conditions (same task mix, same hardware class, same API path) showing a ratio materially different from 2.0 (e.g. ≤1.3×).
- **C4 (agentic coding 64% vs. 38%):** a public replication on SWE-bench Verified (or the same PR-based task set, if released) showing the June snapshot at a pass rate far from 64%, or showing the Opus gap collapsing when both models use the same scaffold and tool budget.
- **C5 (strongest vision model):** a re-run of MMMU/MathVista/ChartQA/DocVQA/AI2D under one harness showing a competitor ahead on the majority, or the MMMU loss widening.
- **C7 (ASL-2):** publication of the thresholds of concern and the model's scores showing a breach; or an external evaluation (UK AISI results, if published; METR) reporting capability above the ASL-3 threshold.
- **Irreducibly unfalsifiable as stated:** "raises the industry bar for intelligence" (no operationalisation of "intelligence") and "grasps nuance and humor" (no measurement). These are recorded as unfalsifiable, not as supported.

### Alternative explanations (at least two, with discriminating evidence)

1. **Prompt/protocol asymmetry, not model superiority (vs. A3).** *Story:* 3.5 Sonnet was evaluated under its most favourable protocol per benchmark (e.g. 5-shot CoT for MMLU, where it scores 90.4%, vs. the 0-shot CoT protocol where GPT-4o wins), while competitors were quoted from their own publications under different protocols or left blank. *Divergent prediction:* under one fixed harness with identical prompts/shots for all models, the cross-vendor gaps shrink or reorder, while the within-Claude gaps (same harness) stay. *Discriminating evidence:* a single-harness replication (e.g. OpenAI simple-evals, or an independent eval suite) with pinned versions and CIs.
2. **Benchmark contamination / format fit, not general capability (vs. A1/A2).** *Story:* the gains reflect overlap between training data and benchmark items (or heavy optimisation to benchmark formats during development), so scores overstate transferable capability. *Divergent prediction:* the model shows much smaller gains on *fresh, post-cutoff* benchmarks and on real-world task suites than on the reported (pre-2024, widely mirrored) benchmarks. *Discriminating evidence:* a decontamination/overlap analysis (never published for this model) or performance on benchmarks created after the April 2024 knowledge cutoff — the October addendum's SWE-bench Verified (33.4% → 49.0% for the upgraded snapshot) is a partial, later data point in this direction, but it concerns a different snapshot and a different eval.
3. **Scaffold and tooling, not the model, drive the agentic-coding gap (vs. A5/A6).** *Story:* the 64% vs. 38% gap reflects a better agent harness, tool set, iteration budget, or prompts given to the newer model (or an older, weaker harness for Opus), not a 26 pp model difference. *Divergent prediction:* swapping harnesses (Opus on the new scaffold, 3.5 Sonnet on the old) moves the pass rates with the scaffold, not the model. *Discriminating evidence:* any release of the eval harness, or a public SWE-bench-style replication with a fixed scaffold for both models.
4. **Style/length preference, not capability, drives the human win rates (vs. A7).** *Story:* raters preferred 3.5 Sonnet's outputs for tone, formatting, or length — qualities the announcement itself praises ("natural, relatable tone") — rather than for correctness. *Divergent prediction:* length-controlled, blinded re-runs show win rates near 50% on correctness-scored tasks while remaining high on style-scored ones. *Discriminating evidence:* a blinded human eval with output-length controls and correctness-based scoring (not reported in this document).

### Author-stated limitations (quoted or closely paraphrased, with location)

- "We still have a lot to learn about the science of evaluations and ideal cadences for performing these tests" (Addendum §3.1) — an explicit admission that the evaluation methodology is immature.
- HHH training may cause capability evaluations to **underestimate** the model's underlying capabilities due to refusals; internal elicitation was used to compensate, and results "may underestimate the capability of an equivalent Helpful-only model" (Addendum §3.2).
- The model "does not trigger the 4x effective compute threshold at which we will run the full evaluation protocol" (Addendum §3.1) — i.e. the full RSP protocol was **not** run for this model; only targeted testing was done.
- The UK AISI tested a "**near-final** model" (Addendum §3.2), not necessarily the shipped artefact.
- The announcement itself states **no limitations at all** — no caveats on any capability claim.

### Analyst-found limitations (not stated by the author)

- No uncertainty quantification, no n, no seeds, no harness, no prompts, no decoding — for any number.
- No training information whatsoever (data, compute, parameters, architecture, method) — the model is a black box; the *cause* of the improvement is never addressed.
- No contamination/decontamination analysis.
- Cross-vendor cells are second-hand, protocol-asymmetric, and incomplete ("—" exactly where comparisons would be most informative).
- No checkpoint/version identifier, so the evaluated artefact cannot be tied to the shipped artefact.
- Safety thresholds and quantitative safety scores are withheld; the ASL-2 classification cannot be checked.
- Human-eval method deferred to the Claude 3 main card §5.5, which is not part of this release; Figure 3 values are not recoverable from the document as fetched (see Unverified).
- The announcement page as it exists today is not the June 2024 original (Aug 2025 update notice; metadata updated 2026-11-13); the benchmark content lives in the addendum, not the announcement, so a reader of the announcement alone sees claims with no numbers.
- Roadmap claims (C9: Haiku/Opus "later this year") are promises, not findings; Haiku did arrive in October 2024, Opus 3.5 never shipped — a reminder that the document's forward-looking statements should not be read as results. (The non-arrival of Claude 3.5 Opus is external knowledge, noted here only as a caution about C9, not as a finding of this deep dive.)

### What to watch next (what would most change the judgement)

1. **The October 2024 addendum** (Claude 3.5 Haiku and Upgraded Claude 3.5 Sonnet) — the immediate successor document: it adds SWE-bench Verified, TAU-bench, and OSWorld with 95% CIs, and re-presents the June numbers. Comparing its protocols and CIs against the June tables is the fastest way to test whether the June gaps survive better measurement. *(Next in the chronological redo; do not conflate its numbers with the June snapshot's.)*
2. **Independent single-harness replications** of the June snapshot (e.g. simple-evals-style runs) with pinned versions and confidence intervals — the direct test of falsifier 1 and alternative 1.
3. **Publication of the UK AISI pre-deployment results** or METR's autonomy assessment — the direct test of C7.
4. **Any release of the internal agentic-coding eval** (task set, harness, n) — the direct test of C4 / alternative 3.
5. **Later Claude 3.5 Sonnet system-card entries** — whether Anthropic's own later reporting revises any June number (revisions would be evidence about the June table's reliability).

### Open questions (from the source; "can't tell" + what would settle each)

- What is the exact checkpoint / API model ID of the evaluated June snapshot? *Settled by:* Anthropic publishing a version identifier or dated snapshot record. **Can't tell from these sources.**
- What were the prompts, harness, decoding settings, n, and seeds for Tables 1–2? *Settled by:* release of eval configurations. **Not reported.**
- Was there any benchmark contamination, and was decontamination performed? *Settled by:* an overlap analysis. **Not reported.**
- What are the safety "thresholds of concern" and the model's scores against them? *Settled by:* publication of the RSP evaluation details for this model. **Not reported.**
- What are the Figure 3 win rates for tasks other than Law/Finance/Philosophy, and what were the rater/task counts? *Settled by:* the rendered Figure 3 or the Claude 3 main card §5.5. **Can't tell from the text fetch (see Unverified).**
- How was "twice the speed" measured? *Settled by:* any latency/throughput methodology. **Not reported.**
- What is the model's knowledge cutoff? **Not reported in the June sources** (the October addendum states April 2024 for both the original and upgraded snapshots — a later document, not a June source; see Unverified).

---

## Unverified / could not verify

Everything in this section is something this deep dive **did not verify against the primary sources read on 2026-11-23**. Items here are not presented as established anywhere in the file above.

### Official links

| Link | Status |
|---|---|
| https://www.anthropic.com/news/claude-3-5-sonnet | **Verified** — full text fetched 2026-11-23 (81 lines). Note: page carries an Aug 28, 2025 update notice and `Updated: 2026-11-13`; it is not the pristine June 2024 original |
| https://www.anthropic.com/system-cards | **Verified** — fetched 2026-11-23; lists "Claude Sonnet 3.5, June 2024" (its link resolves to the addendum PDF below) |
| https://assets.claude.com/fed9cc193a14b84131812372d8d5857f8f304c52.pdf (Claude 3.5 Sonnet Model Card Addendum) | **Verified** — full text fetched 2026-11-23 (559 lines); confirmed as the system-cards "June 2024" link target |
| Claude 3 Model Card (main card, March 2024), §5.5 — referenced by the addendum for the human-eval win-rate method | **Not read in this deep dive** — the win-rate method is therefore unverified here; do not cite this file as establishing how win rates were computed |
| RSP Evaluations Report – Claude 3 Opus (May 2024, addendum ref [23]) | **Not read** — cited only as a reference inside the addendum |
| UK AISI / US AISI pre-deployment results; METR autonomy exploration | **Not published in the sources read** — their existence is asserted by Anthropic; their content is unverified |
| The announcement's internal links (API docs, Bedrock, Vertex AI, ASL-2 page, Thorn, Commerce Dept. MoU) | **Not followed / not verified** — link targets were not opened; only the link text in the announcement was read |

### Numbers and claims that could not be verified against the primary sources

- **Announcement date 2024-06-20 vs. 2024-06-21** — the news page's own metadata says 2024-06-21; secondary chronologies commonly say 2024-06-20. Unresolved; flagged in the header and in §A.
- **"Twice the speed of Claude 3 Opus"** — no latency, throughput, task, hardware, or method is reported in either source. The ratio is unverifiable as stated and is not presented as an established number in this file.
- **Figure 3 per-task human-preference win rates other than Law 82%, Finance 73%, Philosophy 73%** — the addendum's prose confirms only those three; the remaining per-task values (Coding, Documents, Medicine, Creative Writing, Multilingual, STEM, Instruction-Following, Honesty, Vision, Harmlessness, Helpful-Only) could not be reliably recovered from the PDF text extraction, because the label-to-value mapping is scrambled. They are **not** reported as established values in §E (see E3.8). A visual read of the rendered Figure 3 would be needed.
- **"Marked improvement in grasping nuance, humor, and complex instructions" / "exceptional at writing high-quality content with a natural, relatable tone"** (announcement L15–17) — no measurement of any of these is reported; unverified qualitative claims.
- **Knowledge cutoff April 2024** — stated in the *October* 2024 addendum for the original snapshot, **not** in any June 2024 source read here. Treated as unverified for the June snapshot in this file; do not cite this file for the cutoff.
- **SWE-bench Verified 33.4% for the June snapshot** — appears as the predecessor value in the October addendum and in secondary chronologies; it is **not** reported in the June announcement or June addendum and is therefore not presented as a verified June number here.
- **Any parameter count, training compute (FLOPs), training-data composition, architecture, or training method** — not reported in either source; nothing of this kind is claimed in this file.
- **Any error bar, confidence interval, sample size (n), seed, or p-value for any benchmark** — not reported in either source; all §E numbers are single point estimates with incomplete conditions.
- **Any quantitative safety score or threshold value (CBRN, cyber, autonomy)** — not reported; only the qualitative outcome ("did not exceed thresholds", ASL-2) is stated.
- **Exact model version / checkpoint / API model ID for the evaluated snapshot** — not reported in either source.
- **Table 1 column reconstruction** — the PDF text extraction scrambles Table 1's layout; the §E reconstruction follows the stated column order and was cross-checked for consistency against the October addendum's re-presentation of the June numbers, but a visual check of the rendered Table 1 would be needed to rule out a column-assignment error in any single row. Rows most at risk of mis-assignment are the ones with missing cells (MMLU 5-shot, MMLU 5-shot CoT, GPQA Maj@32, GSM8K). If a future visual read corrects a cell, §E must be corrected, not the headline judgement — the judgement already assumes the table is as reconstructed and notes its limits.
- **Secondary-source numbers not used:** no number in this file was taken from a secondary summary (news coverage, blogs, leaderboards) without checking it against the two primary sources above. Where a secondary source was consulted during research (search results), its numbers were used only as a prompt to re-check the primary text, and any number that could not be matched to the primary text is listed in this section instead of being presented as established.

### Method note

This deep dive was produced by **text fetch** (`browser.open`) of the announcement, the system-cards index, and the addendum PDF — not by a live browser visit. Charts in the announcement (if any) and the rendered figures in the addendum (Figures 1–3, including the NIAH plots and the human-preference charts) were read only through their text extraction and captions, not visually. A visual read of the rendered PDF is the main remaining verification step for Figure 3 and for the Table 1 column reconstruction.
