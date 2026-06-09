---
name: scaffold-section
description: The universal scaffold producer for every paper section — foundational and structured. Dispatched by paper-walkthrough at a section's writing handoff, it lays that section's open-item markers into working_paper.md, each carrying a function-prompt and an attributed demonstration quote drawn from the student's own chosen articles. Every scaffold is a synthesis of a pre-built template (section-scaffolds.yaml) and/or customizations from the student's articles — never fabricated from thin air. It never produces draftable text; the student writes every word. Where neither the template nor the student's articles can model a section, it engages the student to find an additional source paper through the research agent (a real, fetched article — never an invented one), which is its one model-escalation instant. Per section; both dispatch contexts (foundational / structured). Status — draft, awaiting teacher-admin critique and educator review.
---

# Scaffold Section

**Last edited:** 2026-06-08 (Cowork — first draft, authored against the paper-scaffolding design of this date: scaffold-section + paper-walkthrough own all-section scaffolding (spec §5.4, §12); the synthesis/no-fabrication rule; demonstrations in the marker block (educator Q1); the no-blueprint research-agent round-trip (educator Q2); a conditional model-escalation instant at the no-blueprint case (educator ruling); the structural fetch-before-use anti-hallucination guard)
*Editing convention: see `00-handoff.md` → "Editing conventions" for editor identifiers and revision-marker rules. This skill also carries a Status section at the bottom recording the draft-vs-promoted lifecycle; the two coexist.*

## What this skill is

`scaffold-section` is the **single, universal scaffold producer for every paper section** — foundational and structured alike (architecture spec §5.4, §12). It is a **paper skill** dispatched by `paper-walkthrough` at the moment a section is ready to be written. By completion of one dispatch, the named section's **open-item scaffold** has been laid into `working_paper.md`: an ordered set of one-line, ASCII, ID'd markers, each carrying the *function* of a passage the student must write and an *attributed demonstration quote* showing how a published author did that — drawn from the student's own chosen articles.

It does exactly one kind of thing, and it is the **consolidated home of the demonstration-versus-production discipline** (decision-history §8.1): it supplies *structure and demonstrations*, never *content*. **It never produces draftable text** — no sentence it emits is the student's to keep; the function-prompts are deleted as the student writes, and the demonstration quotes are others' published words, attributed. The student writes every word of the section themselves (the direct-edit / Google-Docs round-trip, §5.4). There is no AI writing support after the scaffold is laid; the student writes, and a human reviews (decision-history §8.2).

**A scaffold is a synthesis, never thin air.** Every scaffold is built from a pre-built template (`section-scaffolds.yaml`, the educator-maintained library in curriculum-articles) **and/or** customizations drawn from the student's own chosen articles — the *and/or* is load-bearing: the normal case **blends both**. The agent never invents a scaffold from nothing; where it has no usable blueprint it engages the student to find one (see "The no-blueprint case").

## When this skill fires

Dispatched by `paper-walkthrough` (architecture spec §12; contract §4.4) when a section reaches its writing handoff — the cross-track baton: a foundational stage-skill (or the research agent for the execution-regime structured sections, per R10) finishes the section's *substance* and returns the **`writing_handoff` outcome** (contract §5.1); `paper-walkthrough` reads the resulting pending-scaffold marker from `project_paper_status.md` and dispatches this skill. `scaffold-section` **trusts the dispatch** — `paper-walkthrough` is authoritative on preconditions (the §7 dependency map; the seal logic — it never dispatches scaffolding for a sealed-cycle section), and any precondition violation reaching this skill is a contract bug, not a runtime case (contract §4.4).

It is also dispatched in **re-surface mode** (below) and on a **stale re-scaffold** (a planning-regime revision rippled into this section; `paper-walkthrough`'s stale gate).

**Dual-context dispatch (contract §4.4), mutually exclusive — `section_kind` selects which fields populate:**

- **Structured** (methods / results / discussion): `target_section`, `section_kind = structured`, `template_ref` (the family skeleton in `section-scaffolds.yaml`), `paper_structure_entry` (the cycle's `paper_structure.md` row — derived organization + agent permissions), `reference_articles_for_section` (the **Method Exemplar Articles**).
- **Foundational** (research problem / research questions / literature review & synthesis / gap / hypotheses): `target_section`, `section_kind = foundational`, `template_ref` (the canon skeleton), `reference_articles_for_section` (the **topic-area Project Reference Articles**); no `paper_structure_entry`.

**Not a rationale trigger, and not a blanket model-escalation moment.** Routine scaffolding — selecting a template, reading the student's articles, matching function-rows, attaching demonstrations, laying markers — is structural synthesis, not the high-judgment evaluative reasoning the careful tier is reserved for (the same negative as `derive-paper-structure`). It carries **one conditional escalation instant**: the no-blueprint case (below).

## The two modes of synthesis

For each function-row of the section's skeleton, the scaffold is produced by one or both of:

- **Surface + customize a pre-built template.** Take the `template_ref` skeleton from `section-scaffolds.yaml`. For a **structured** section, reconcile it with the `paper_structure.md` row's derived organization and the student's study (add/prune/reorder rows to fit). For a **foundational** section, take the canon skeleton **and customize it against the student's articles**. Both kinds get customized — the template is never surfaced bare.
- **Derive from the student's chosen articles.** Read the student's articles (their extracted text; the staged PDFs where needed) and model the skeleton on how *those* papers build this section, where the template is silent or the study is unusual.

The normal scaffold is a synthesis of both. The skeleton rows are **function-prompts only** ("name the data source and design"; "operationalize each variable") — never the content of the sentence.

## The internal phases

**Phase 1 — Build the skeleton (synthesis).** Produce the section's ordered function-rows from the template and/or the student's articles per the two modes above; reconcile to the derived organization (structured) or the canon order (foundational).

**Phase 2 — Attach demonstrations (from the student's own articles).** For each function-row, locate in the student's chosen article(s) the passage that performs that function, and attach it to the marker as a **direct, attributed quote** — the primary source is the section's informing model (the inherited Method Exemplar Articles / topic-area Project Reference Articles); the student's other articles are the supplementary pool. Demonstrations are how published authors do it, shown so the student understands what to write — never a sentence about the student's own topic to copy. AI-composed demonstrations are **generic and fallback-only** (illustrating the technique on a neutral, unrelated example so nothing is liftable into the student's paper). *(Educator ruling: the demonstration quotes ride in the marker block so they travel with the scaffold into the student's Google Doc; attributed quotes are STS-fine, and the student typically won't even cite them — they are demonstrations, deleted as the student writes their own sentences.)*

**The lingering-quote bound.** Because a demonstration quote sits inside the `[[ … ]]` marker, the intended path is that it is deleted as the student replaces the marker with their own prose. The safety of the case where a student leaves a marker *undeleted* rests on **attribution**: a surviving demonstration is an attributed quote from a published source, which is STS-fine even if it lingers — it never becomes an unattributed AI-written sentence in the student's paper. Detecting and flagging a marker block that survived into otherwise-written prose is a **desired `compile-working-paper` responsibility to be contracted when that skill is authored** (it currently reconciles *dropped* expected items, the inverse of a *kept* marker); the STS line does not depend on that flag — the attribution leg holds it on its own. *(Forward-binding: see open item 1 and the 2026-06-08 decision-log.)*

**Phase 3 — Lay the markers (the only write).** Write the section's open-item markers into `working_paper.md`, in derived order, each of the form:

```
[[ Scaffold item - your work goes here. <function prompt>. (id: <section.n>)
   How a published study does this — <author, locator>: "<attributed quote from the student's article>" ]]
```

The student then writes their prose directly, deleting each marker as they go (direct-edit / Google-Docs round-trip). `scaffold-section`'s job ends at the markers; it does not accompany the writing.

**Re-surface mode.** On a dispatch to re-surface (an open item was dropped without prose — detected by `compile-working-paper`'s reconciliation against the template), re-lay the missing item's marker without disturbing any written prose. The template is the source of truth for what items exist (§5.4).

## The no-blueprint case (the conditional model-escalation instant)

The edge case the no-fabrication rule exists for: neither the template **nor** the student's chosen articles can model a section — a previously-unapplied method, or a project whose *topic is a method* the student is putting a new wrinkle on, so no article in the corpus shows how to write its methods/data sections. The skill does **not** dead-stop, and it **never invents a filler article or a fabricated structure**. Instead:

1. It customizes from whatever the template and the student's articles *can* supply, to the greatest extent possible.
2. For the rows it cannot model, it **engages the student to find an additional source paper**, routing through the research agent: it composes a concrete handoff prompt the student pastes (`research` + the specific ask — "find a published paper that reports this kind of study so I can model the section structure on it"), the research agent finds and **processes the real article** (fetches + stages it via `fetch-articles`), then hands the student back with a composed return prompt (`mentor`/the paper flow), and `scaffold-section` customizes the skeleton from the newly-staged article. *(Both handoff directions carry student-usable composed prompts — educator Q2.)*
3. Only if even sourcing a real paper cannot yield a usable blueprint does it route to a **human teacher/mentor** (last resort). The unmet gap rides `advisory_notes`.

**This is the skill's one model-escalation instant** (`model-escalation`, the no-blueprint entry): judging which published precedent can serve as a methodological blueprint for a novel approach is literature-aware, possibility-space reasoning bound up with the student's distinctive contribution — and it is the moment most exposed to confabulation. The skill references `model-escalation` here and surfaces the upgrade recommendation before making the blueprint judgment.

**The structural anti-hallucination guard (the actual guarantee).** Escalation reduces the risk of a fabricated article but is **defense-in-depth, not the guarantee.** The guarantee is structural: **a blueprint must be a real article that physically exists in the corpus** — a fetched, staged file with real text. The research agent must *actually retrieve* the supplementary article (`fetch-articles`; 403-is-final; no paywall circumvention); an article that cannot be fetched is treated as **not found**, and the engage/human-referral path fires. The skill never builds a scaffold from, or cites, an article it did not retrieve — so a hallucinated article can never become a blueprint. The review agent's spot-checks (V5 — "are these links real articles?") and the Friday corpus-integrity audit (V1c) are the backstops.

## The scripted moments

Per the established convention: adapt wording and re-point examples to the student's project; preserve the core steps and their order; concise, example-driven, no rationale lectures.

### Moment 1 — Presenting the scaffold (Phase 3)

> *"Here's the structure for your [section] — each line says what that sentence should do, with a quote showing how [their article(s)] did it. Here's how to use it:*
> *1. Write your own sentence for each line, directly in your Google Doc.*
> *2. Delete each prompt as you write it.*
> *3. The quotes are just demonstrations — not your sentences, and they don't go in your paper, so delete those too.*
> *4. Whenever you're done writing and want to save your changes back to your working paper, follow the steps in your writing-and-saving guide: [link]."*

### Moment 2 — The no-blueprint engage (the no-blueprint case)

> *"For [section], none of your papers quite show how to structure this — which makes sense, since [your method is new here / you're putting a new spin on the method]. Rather than make something up, let's find a published paper that did a study like this so we can model the structure on it. Copy this and paste it to your research agent — it'll track one down and get it ready: [composed prompt]. When it sends you back, we'll build the scaffold from the real paper."*

## What this skill writes to the workspace

`working_paper.md` only: the section's open-item markers (Phase 3) — function-prompts + attributed demonstration quotes; **never prose, never the student's content**. It is a sanctioned writer of `working_paper.md` (contract §4.6: scaffold-section writes the markers). It does **not** write `project_paper_status.md` (the walkthroughs own it; `paper-walkthrough` updates `section_state` on return) or `decisions.md` (not a rationale trigger). Return payload per contract §5.1: `completion_status`, `workspace_writes` (the markers laid into the named section), `newly_stale_sections` empty (scaffolding stales nothing), `decisions_appended` empty, and `seal_state_change` / `new_cycle_decision` / `writing_handoff` all `null` (this skill *receives* the dispatch the handoff triggered; it sets none of them). `advisory_notes` carries any no-blueprint gap (the article the student still needs) or a residual unmodeled row.

**Partial returns.** The natural pause is the no-blueprint round-trip — a return with the modelable rows scaffolded and the rest pending the research-agent errand is `partial`, resuming on the student's return with the staged article. Resumption is file-authoritative: which of the section's expected items already have markers in `working_paper.md` (the template defines the expected set).

## Edge cases

**No usable blueprint (the signature case).** Handled by the no-blueprint flow above: customize to the max, engage the student to source a real paper via the research agent (composed prompts both ways), human referral last; never fabricate. The conditional escalation instant fires here; the fetch-before-use guard ensures the blueprint is a real article.

**Off-menu family (item 13 in `section-scaffolds.yaml`).** No pre-set template exists; the skill builds the skeleton live from one of the student's chosen papers (reading its structure with the student), and flags the gap so the educator can vet and, if recurring, add a family. This is the same synthesis discipline with the template term empty.

**Re-surface after the student dropped a marker.** `compile-working-paper` detects an expected item with neither prose nor marker; on a re-surface dispatch this skill re-lays just that item's marker, leaving written prose untouched. The student can write and delete freely (§5.4).

**A stale re-scaffold (planning regime).** When a revision rippled into this already-written section, `paper-walkthrough`'s stale gate dispatches this skill to re-lay the affected items; the student rewrites directly. The skill never edits the existing prose — it only re-lays structure.

**A sealed-cycle section.** `paper-walkthrough` does not dispatch scaffolding for a sealed cycle's sections (the seal logic, §6); the skill trusts that and does not guard the seal itself.

**The student asks the skill to write or fix a sentence.** The negative-case rule (decision-history §8.2 / X4): it declines politely and protectively — an AI touching the report prose would breach the STS AI rules and risk the student's eligibility — and points them to their teacher or another human mentor. The skill's job is structure and demonstrations; the words are the student's.

## Where this skill lives in the architecture

A **paper skill** dispatched by `paper-walkthrough`, bundled in the project-mentor distribution (curator-immune, update-refreshed per platform primer §7). It is the universal scaffold producer for all sections (spec §5.4, §12) and the consolidated home of the demonstration-versus-production discipline. It reads the educator-maintained `section-scaffolds.yaml` (curriculum-articles) and the student's `reference_articles.md` + staged articles; it writes only the markers into `working_paper.md`. It is **not** a rationale trigger and **not** a blanket model-escalation moment; it carries a **single conditional escalation instant** (the no-blueprint case). Upstream it is reached by the `writing_handoff` (foundational sections) or the research agent's R10 handoff (structured sections); alongside it, `compile-working-paper` reconciles the living document and re-surfaces dropped items; downstream, the student writes directly and a human reviews — there is no AI writing-support skill.

## Status

**Draft, authored 2026-06-08 (Cowork)** against the architecture specification (§§5.1, 5.4, 6, 7, 12), the dispatch contract (§§4.4, 4.6, 5.1), `design/regeneron-sts-reference.md` §3a (the editorial line), `section-scaffolds.yaml` (the template library), `cross-agent-obligations` (R10/R11 + the new scaffold↔research supplementary-article round-trip), decision-history §8.1/§8.2 (demonstration-vs-production; the negative-case rule) and §9.5 (AI fallibility), the `model-escalation` meta-skill (the no-blueprint instant, added this date), and the four educator rulings of this date (demos in the marker block; the research-agent round-trip with composed prompts both ways; the conditional escalation instant; the fetch-before-use anti-hallucination guard).

**Teacher-admin critique passed 2026-06-08** (zero blocking, one substantive, two minor — the cleanest first-draft skill of the loop). Dispositions applied: F1.2 (the lingering-quote bound now rests on the attribution leg; compile-working-paper flagging recorded as a forward-binding dependency, not an existing guarantee); F5.1 (open item 1 now requires the reconciler to treat the marker as a two-line unit keyed by the line-1 `id:`); S2 ("move" usages replaced per the vocabulary ruling); S1 (Moment 1 trimmed). **Educator voice-read complete 2026-06-08** — Moment 1 restructured as numbered instructions (ambiguous "it" and "where things stand" made concrete); Moment 2 approved as-is. Committed.

### Open authoring items

**Convention** (per dispatch contract §11): items remain numbered while open; resolved items are retained with strikethrough, their original wording preserved, and a resolution date.

1. **Marker-block format for the demonstration.** The two-line marker block (function-prompt line + demonstration line) is a draft form; confirm it survives the Google-Docs round-trip cleanly (ASCII, the `[[ … ]]` delimiters, the quote on its own line) and that `compile-working-paper`'s `id:`-anchored reconciliation reads it. **Requirement for the `compile-working-paper` author:** the reconciler must treat the marker as a *single two-line unit keyed by the line-1 `id:`* — so a Google-Docs round-trip that reflows or splits the block cannot orphan the demonstration line from its id-anchor (reconciliation precedence is `id:`/heading, and the `id:` lives only on line 1). Pin against `compile-working-paper`'s authoring; see also the F1.2 forward-binding flag (detect a kept marker surviving in written prose).
2. **The scaffold↔research-agent round-trip contract.** The no-blueprint errand to the research agent (find + stage a real supplementary article, hand back) is recorded as a cross-agent obligation this date; the exact composed-prompt shape and the return signal are pinned with the research agent (Tier 2) authoring and `paper-walkthrough`.
3. ~~**Moment wording.** Both moments await the educator's voice read, per precedent.~~ **Resolved 2026-06-08** (educator voice-read): Moment 1 restructured as numbered instructions with the ambiguous referents made concrete ("it" → "write your own sentence … in your Google Doc"; "where things stand" → "what's done and what's still open"); Moment 2 approved as-is.
4. **Re-surface vs. stale re-scaffold dispatch distinction.** Whether `paper-walkthrough` distinguishes a re-surface (dropped marker) from a stale re-scaffold (upstream revision) by dispatch field or the skill infers from `section_state`/`stale_verdict` — pin at `paper-walkthrough` authoring (the §11 cluster).
5. **The writing-and-saving guide (Moment 1, step 4's `[link]` target).** Step 4 points the student to a standing guide for the save-back round-trip (open in Google Docs → write → export as Markdown → save back to the agent folder so `working_paper.md`/`project_paper_status.md` update). This is a fixed, repeated procedure that belongs in one always-accessible place, not re-narrated per Moment. **Open design fork** (educator, 2026-06-08): (a) a static guide doc shipped with the `research-curriculum-workspace-template` repo (recommended interim — version-controlled, identical for everyone, the natural `[link]` target); vs. (b) a dedicated step-by-step skill invoked each write-session; vs. (c) front-matter in `working_paper.md` itself. The skill option earns its place only if it *automates* the save→reconcile (detect the exported file, auto-run `compile-working-paper`) — that automation question is `compile-working-paper`'s to settle. **Pin the guide's home/form and resolve the `[link]` placeholder at `compile-working-paper` / `paper-walkthrough` authoring and the Lessons 1–2 file-handling beat.**
