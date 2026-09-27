---
name: autopilot
description: Autonomously execute a complete Buckeye rubric task end-to-end: triage the assigned document environment, write a professional rubric-busting prompt, build the golden deliverable, repair the auto-generated rubric, and grade model responses against both with scored justifications. Invoke this skill when you need to run a full Buckeye evaluation task from environment exploration through final grading with no further human direction.
---

# Autopilot: End-to-End Buckeye Rubric Task Workflow

This skill encodes the complete Buckeye workflow for creating and evaluating a rubric task on the platform. When invoked, follow every phase in order, top to bottom, until the deliverable is complete. Treat every instruction in this file as an operating procedure.

## Phase 0 — Orientation

A Buckeye rubric task has five sequential phases:

1. **Triage the environment** — understand the assigned document set, classify each file, and confirm the environment is rich enough to support a hard task.
2. **Write the prompt** — craft a professional, role-grounded prompt that is solvable only with the input files.
3. **Build the golden deliverable** — produce the ideal answer from only the prompt + input files, scoring ≥ 0.97 against your own rubric.
4. **Repair the rubric** — take the auto-generated rubric and harden it against the golden, ensuring every criterion is observable, atomic, self-contained, and binary.
5. **Grade model responses** — score each of the 8 model responses against the rubric, justify every failure, and confirm the reference-model averages hit target difficulty thresholds.

The target difficulty: the strong reference model averages ≤ 0.50 and the weaker model averages ≤ 0.20 across k=4 runs per model, with a ≥ 0.20-point gap between them.

---

## Phase 1 — Triage the Environment

### 1.1 Map every document

List every file in the assigned environment. Do not skip anything. For each file record:

- **Filename** (exact, as it appears on disk)
- **Format** (PDF, XLSX, XLS, DOCX, PPTX, TXT, CSV, PNG/JPEG/WebP/GIF, ZIP/archive)
- **Size** (KB or MB)
- **Length tier** (short: 1–5 pages, medium: 5–20 pages, long: 20+ pages)
- **Domain folder** (audit_liaison, customer_service, district_clerk, facilities_and_slifts, finance_and_billing, harbor_operations, information_systems, moorage_counter, public, training_and_certification, waiting_list_administration, scoring_notes)
- **Quick content summary** (2–3 lines)

The environment must contain at least **10–20+ files that pass the deletion test** (see 2.11). If the environment is too thin, you cannot build a valid task — flag this immediately.

### 1.2 Verify file hygiene

- **Allowed types only**: PNG, JPEG, WebP, GIF, PDF, XLSX, PPTX, DOCX. No CSV, no TXT, no archives, no links-as-files. If you encounter a CSV or TXT, convert it to XLSX before proceeding.
- **Every file opens and renders cleanly** in its native application or a standard viewer. Flag any file that is corrupt, encrypted, or password-protected.
- **No PII**: personally identifiable information must be scrubbed. Same real-world entity = same stand-in name across every file.

### 1.3 Confirm environmental richness

The environment must satisfy all of the following:

- ✅ Uses **at least 3 distinct file formats** and a mix of document lengths (short, medium, long).
- ✅ The task forces the model to **work across many files** — the answer does not sit in one file.
- ✅ Files **reference each other** (cross-tab IDs, shared entity names, linked dates).
- ✅ File names look like real-world artefacts (e.g. `amendment_log_2022_2026.csv`, `system_configuration_report_2025-02.pdf`), not `file1.csv` or `prompt_attachment.pdf`.

### 1.4 Scoring notes as domain context

The `scoring_notes/` folder contains short `.txt` memos written during prior audit passes (e.g. foot-month working notes, amendment-window analysis, calibration tally, offer-sequence check). These are domain-knowledge artefacts, not task instructions. Read them to understand the professional context and workflow conventions of the mooring/wait-list environment, but do not treat them as the source of truth for any deliverable value — they are working scratch, not audited final figures.

---

## Phase 2 — Write a Strong Prompt

A prompt never stands alone. The input files decide how hard the task can be; the prompt decides what files you need. They are developed together.

### 2.1 The Six Core Attributes

Every prompt must satisfy **all six** before it qualifies:

1. **Unambiguous** — Clear phrasing is required, but the content of the question is not given to you. You do not need to spell out the solution path. The model must figure out what to do and how to do it. Any failure caused by ambiguity is your fault, not the model's.
   - If the answer requires information the model does not have, state what to do: "flag it as missing" or "state you cannot determine it."
   - Spell out exact output: format, file name, quality bar.

2. **Professional Role & Context** — Name the persona, audience, and stakes:
   - **Persona**: a specific professional role (e.g. "You are a moorage facilities analyst at the district harbor").
   - **Audience**: who consumes the output (e.g. "submit this to the Harbor Commission for its March 2026 meeting").
   - **Stakes**: why it matters (e.g. "the Commission will use this to set rates before the April filing deadline, so omissions or errors will force a rate correction later in the season").

3. **Realistic & Not Contrived** — Reads like a real email, memo, or brief. No puzzles, no roleplay, no "ignore X for no reason" tricks. Difficulty must come from professional reality, not arbitrary constraints.

4. **Timelessness (Relative Dating)** — State a reporting cutoff near the top and hang every date off it, with the year included on every date:
   > "As of the reporting cutoff of 31 March 2026, ..."
   > "All figures are as of FY 2025 (1 July 2025 – 30 June 2026)."
   Never use "recently," "currently," "last quarter," "this year," or any future date. The cutoff must be in the past.

5. **Clear Deliverable** — Spell out the output: format, file name, quality bar.
   - E.g. "Produce a downloadable `.xlsx` workbook named `moorage_rate_study_2025.xlsx`..." or "Save the briefing note as `WL-02770_waitlist_status_memo_2026-03.docx`."

6. **Clear Constraints** — List the must-nots and limits that make the task hard.
   - Must-nots: "Do not rely on any external data or web search. Do not fabricate vessel names, slip assignments, or monetary figures. Cite each source file and tab/cell in a Source Log."
   - Limits: "If a required figure cannot be traced to an input file, leave the cell blank and note 'not sourced' in the Source Log rather than estimating."

### 2.2 Difficulty Levers (Complexity without bulk)

Make the prompt harder without making it longer:

| Lever | What to do | Example |
|-------|-----------|---------|
| **Conflicting Constraints** | Give two rules that can't both be fully satisfied; the model must trade off. | "Produce the report in Excel AND keep the underlying data visible for quarterly audit review" — forces structured tabs, not just formulas. |
| **Implicit Variables** | Include a small detail that changes the answer, but don't call attention to it. | "Apollo's annual rate is $10,800 but the agreement was signed 15 September 2025 (mid-season)" — model must pro-rate. |
| **Data Reconciliation** | Provide messy data where some rows don't match across files; model must exclude, not invent. | Some slip IDs in the inventory don't appear in the billing ledger — calculate revenue anyway, flag mismatches. |
| **Domain-Knowledge Outliers** | Include a value that looks normal but is clearly wrong to an expert; don't flag it. | A slip's recorded length of 99 ft in a basin whose maximum is 60 ft. |
| **Creative Tension** | Two technical asks that pull against each other. | "Show quarterly variance as a dynamic formula" AND "freeze the final row as a non-editable summary total." |

Never use tricks and riddles. Difficulty = professional reality.

### 2.3 Artificial vs. Real Difficulty

- ❌ Artificial: "Pretend Dr. Kamal is Dr. Smith and hours are 50 minutes long" → tests instruction-following, not domain skill.
- ✅ Real: "Dr. Kamal started June 1st; his salary is $99,604 — calculate pro-rated 2023 potential" → tests payroll logic.

Apply this test to every constraint. If the constraint is arbitrary, remove it.

---

## Phase 3 — Build the Golden Deliverable

### 3.1 Golden rules

- **Source of truth**: Build only from the prompt + input files. No outside data, no web search, no prior knowledge the model didn't have.
- **Every value verified**: Every number, date, citation, and cell must be traced back to a source file. If you cannot trace a figure, remove or correct it.
- **File type matches the ask**: Spreadsheet → `.xlsx`, report/memo → `.docx`, deck → `.pptx`, script/data → the actual `.py`/`.json` file.
- **Every explicit ask appears**: Walk the prompt requirement by requirement and confirm each one is actually present in the file.
- **Client-ready quality**: Layout, headings, labels, and styling are consistent and professional. It should look like something you could hand to a client as-is.
- **Self-scoring ≥ 0.97**: The golden must score at least 0.97 against your own rubric. If it doesn't, the rubric and/or golden are wrong.

### 3.2 AI assistance is allowed on the golden

Unlike the prompt and rubric (which must be entirely your own work), AI assistance on the golden is permitted — **but you own correctness**. For every value the AI drafts:
- Trace it to a source file and confirm it is correct.
- Fix anything the tool invented, got wrong, or skipped.
- Never accept a value you cannot verify.

### 3.3 Deliverable completeness checklist

Before proceeding to the rubric:

- [ ] File type matches what the prompt asks for.
- [ ] Every explicit ask in the prompt appears in the deliverable.
- [ ] Implicit expectations a domain expert would assume are present.
- [ ] File opens and renders cleanly in its native application.
- [ ] Every number, date, citation, and cell is verified against input files.
- [ ] Built only from the prompt and its input files (no external data).
- [ ] Layout, headings, labels, and styling are consistent and professional.

---

## Phase 4 — Repair the Auto-Generated Rubric

The auto-generated rubric is a starting point, not the final artifact. You must repair it against the golden.

### 4.1 The Seven Rubric Properties

**1. Binary** — Each criterion resolves to a single pass or fail. No partial credit, no sliding scale. Each criterion can fail for one clear, specific reason.

**2. Observable** — Every criterion checks something visibly present in the response. Grade what the model produced, not the process you imagine it followed. No invisible mental processes ("considered," "understood," "knew"). Rewrite to observable output, e.g. "Labels exercises as beginner, intermediate, or advanced."

**3. Self-contained** — Specific values, limits, and constraints are stated directly in the criterion. No "as mentioned above," no "refer to the prompt," no hidden lookups. A grader should never have to look elsewhere to apply the rule.

**4. Atomic** — Every criterion checks exactly one thing. If a rule has two parts joined by "and," "also," or commas, split it into separate criteria.

**5. Objective: no vibe words** — "Good," "clear," "helpful," "appropriate" are removed. Each criterion uses numbers or binary facts that can't be argued about. Two different graders should never disagree on the outcome. E.g. "Lists at least three exercise types" instead of "provides good variety."

**6. Verifiable** — You can point to evidence in the response that proves each rule was met. If you can't point to it, you can't grade it.

**7. Aligned** — Every criterion traces back to something the prompt asked for, explicitly or implicitly. If you can't point to the part of the prompt or the golden deliverable a criterion comes from, it doesn't belong in the rubric.

### 4.2 Row structure

Every row has these fields — **no row is missing a field**:

| Field | Description |
|-------|-------------|
| **ID** | Unique identifier (e.g. R001, R002) |
| **Criterion** | The observable check, stated as something the model either did or did not do |
| **Weight** | On the −100 to 100 scale |
| **Implicit/Explicit** | Whether the criterion is stated in the prompt (Explicit) or assumed by a domain expert (Implicit) |
| **Category** | One of: Instruction Following, Formatting & Aesthetics, Reasoning, Accuracy, Completeness, or Professional Standards |
| **Rationale** | Why this criterion matters and what failure looks like |
| **Citation** | The specific source — prompt line, golden file/tab/cell — that justifies the criterion |

No unexplained abbreviations anywhere in the rubric.

### 4.3 Weights rules

- Every weight sits on the **−100 to 100** scale.
- **Positive weights** reward what the response must get right; **negative weights** (down to −100) penalize specific mistakes.
- Weights reflect what matters to a domain expert. Criteria that catch serious or model-breaking failures carry higher weight.
- Weights are **not all identical** — they distinguish critical from minor criteria. If every criterion has the same weight, the rubric can't tell a serious failure from a trivial one.
- **30–40% of total weight** sits in **Formatting & Aesthetics**; **60–70%** sits in **Reasoning & Accuracy** (reasoning, accuracy, completeness). Sum the weights per category and check the split before submitting.
- Exactly **3–5 negative (penalty) criteria** (max 5).
- **Avoid double negatives**: don't pair a negative weight with a criterion phrased as something the model failed to do. Instead, state the mistake as something the model **did** (e.g. "States the wrong RMD deadline") and give it a negative weight, so a match means points are subtracted.

### 4.4 Coverage check

- **30–45 criteria** total, including 3–5 negative (penalty) criteria, with no filler rows.
- Every explicit request in the prompt is covered by a criterion.
- Every constraint (format, length, tone, sources) has a criterion.
- Implicit expectations a domain expert would assume are captured.
- File conflicts, rule branches, calculations, and key extracted facts are all covered.
- No redundant rows that re-check the same thing.
- The rubric includes both explicit (compliance) and implicit (capability) criteria.

### 4.5 Sanity check — grade the extremes

- **The ideal response** would earn full marks unambiguously (score = 100 or max possible).
- **A non-ideal response** loses points for substantive misses, not only surface polish.
- Formatting criteria support usability and auditability rather than taste.

---

## Phase 5 — Grade Model Responses

### 5.1 Scoring and justification protocol

For each of the 8 model responses (2 models × k=4 runs):

1. **Apply the rubric row by row.** For each criterion, determine pass or fail and assign the weight.
2. **Compute the total score** as a fraction of the maximum possible (normalize if positive and negative weights are mixed).
3. **For every failed criterion**, write a justification that includes:
   - The failure mode tag (see §5.2 below).
   - The specific wrong values quoted from the model's output.
   - The exact locations named (rows, cells, tabs, sections, line numbers).
   - The downstream consequence (why this failure matters).

### 5.2 Failure modes

**Failure Mode 1 — Extraction Failures** (the model has the right data but fails to pull it correctly from messy input files):

- **Hallucination** — Invents data to fill a gap instead of flagging it. E.g. makes up a "Country of Origin" for a blank cell rather than flagging it as missing.
- **Omission** — Misses a critical detail buried in a footnote, sub-tab, or appendix.
- **Misinterpretation** — Reads the data but gets the context wrong. E.g. treats a "Projected 2026" column as "Actual 2025."

**To induce extraction failures**: Bury a critical fact in a footnote, sub-tab, or appendix. Mix projection columns with actuals in the same file. Leave gaps that look fillable — the model will fill them rather than flag them.

**Failure Mode 2 — Reasoning Failures** (the model has the right data but does the wrong thing with it):

- **Dependency Collapse** — Solves Step 1 correctly but forgets Step 2 depends on it; a cascade of errors flows from the first mistake.
- **Constraint Violation** — Ignores a "must not" in order to satisfy a positive goal. E.g. schedules overtime to hit a deadline despite an explicit no-overtime rule.
- **Invalid Inference** — Makes a logical leap that's professionally unsound. E.g. assumes Revenue = Profit when calculating margins.

**To induce reasoning failures**: Stack 3+ dependent calculations so a single error cascades. Add one negative constraint that conflicts with a positive goal. Require domain knowledge to spot an invalid inference — don't flag the error in the prompt.

**Failure Mode 3 — Formatting & Deliverable Failures** (the reasoning is correct but the output is packaged in a way that makes it unusable — right answer, wrong delivery):

- **Wrong Output Format** — You asked for a downloadable `.xlsx` and it returned a markdown table in the chat.
- **Structure Mismatch** — You asked for a memo with an Executive Summary and it wrote a casual email without headers.
- **Formula Stagnation** — You asked for dynamic Excel formulas and it hard-coded the calculated values, breaking future updates.

**To induce formatting failures**: Demand a specific file type (.pdf, .xlsx), name exact tab order, require dynamic formulas rather than values, or specify a precise template (firm-specific memo header, defined column structure).

### 5.3 Scoring thresholds

Verify the reference-model averages meet these targets:

| Model | Target |
|-------|--------|
| **Strong reference** | ≤ 0.50 average across k=4 runs |
| **Weaker reference** | ≤ 0.20 average across k=4 runs |
| **Gap** | ≥ 0.20 points between the two models |

- **If the models succeed** (scores too high), go back and **iterate on your prompt** — make it harder, add complexity, tighten constraints.
- **If the gap is too small**, the prompt isn't differentiating the models — revisit the failure modes and make sure serious errors carry heavy enough weight.
- **If the models fail** (scores in the right range but the golden doesn't score ≥ 0.97), fix the rubric or the golden first.

### 5.4 Final QC

Before submitting, run quality control on:

- The golden file name and attachment.
- Every file in the environment that was used as input is listed in Required Files.
- The prompt and rubric are entirely your own work (no LLM-generated content).
- Any per-file justification for every input file documents: filename, format, contents, why this file exists, and an honest **Load-bearing: Yes/No**.
- The environment uses ≥ 3 distinct file formats and a mix of document lengths.
- The task forces work across many files (answer doesn't sit in one file).

---

## When to Use This Skill

Load this skill when assigned a Buckeye rubric task and you need to complete the full evaluation lifecycle — from exploring the assigned document environment through producing the final scored grading — entirely autonomously with no further human guidance. Say "use the autopilot skill" to trigger it.
