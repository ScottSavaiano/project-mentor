---
name: paper-walkthrough
description: The Paper-track keystone — owns the student's paper-writing position and the integrity of the living paper. Owns the dependency/stale-detection map and enforces the hard stale-rewrite gate within a cycle's planning regime; knows the seal logic and refuses to rewrite sealed-cycle sections; owns the scaffolding-and-writing of ALL sections (foundational and structured) by dispatching scaffold-section at each section's writing handoff (reached from a foundational stage-skill's writing_handoff or the research agent's R10 handoff); calls the mechanical compile-working-paper. Writes section_state and stale_verdict; coordinates with project-walkthrough only through the shared state file. Status — draft, awaiting teacher-admin critique and educator review.
---

# Paper Walkthrough

**Last edited:** 2026-06-10 (Cowork — first draft authored against the architecture specification (§§5, 6, 7, 9, 10, 12) and the dispatch contract (§§2, 3.2, 4.4, 5, 6, 7, 10, 11) as the paper-track keystone, paired with `project-walkthrough`; resolves the re-surface-vs-stale-re-scaffold dispatch distinction. **Teacher-admin critique applied 2026-06-10:** the `stale_verdict`-ownership seam resolved per F2.1 — `project-walkthrough` populates `stale_verdict` via the §7 lookup; this walkthrough owns the **gate** and **clears** entries; `stale_verdict` and `pending_scaffold` are **shared** two-writer fields)
*Editing convention: see `00-handoff.md` → "Editing conventions" for editor identifiers and revision-marker rules. This skill also carries a Status section at the bottom recording the draft-vs-promoted lifecycle; the two coexist.*

## What this skill is

`paper-walkthrough` is the second **keystone** skill (architecture spec §12). It owns the student's **Paper-track position** and the integrity of the **living paper** (`working_paper.md`). Where `project-walkthrough` is dispatch-heavy, this skill is **gating-heavy**: most of its work is reading the dependency map (architecture spec §7), enforcing the **hard stale-rewrite gate** within a cycle's planning regime, and knowing the **seal logic** (§6) so sealed-cycle sections are never rewritten. Its dispatch surface is narrow — it dispatches **`scaffold-section`** (the universal scaffold producer) at each section's writing handoff, and **calls** the mechanical `compile-working-paper`. There is no AI writing-support skill: after the scaffold is laid, the student writes directly and a human reviews (decision-history §8.2).

It coordinates with `project-walkthrough` **only through the shared state file `project_paper_status.md`** (architecture spec §10; contract §10). The two never call each other directly.

## When this skill fires

The mentor reads `project_paper_status.md` at conversation start; the active walkthrough is the one the student's intent matches (contract §10). **Paper-track activity routes here:** writing or revising a section, a writing handoff arriving from the project track or the research agent, a stale gate that must fire, or a request for a paper-state view. On entry the active `paper-walkthrough` parses the state file, reads its own fields (`section_state`, `stale_verdict`) and the project-owned position fields read-only, and acts — dispatching `scaffold-section`, firing the stale gate, refusing a sealed-cycle rewrite, or calling `compile-working-paper`.

This skill is **not** a rationale trigger and **not** a model-escalation moment. Its one escalation-bearing dispatch target, `scaffold-section`, carries its own single conditional instant (the no-blueprint case); this walkthrough does not.

## The position model — how state resolves to an action

`paper-walkthrough` derives its state from `section_state`, `seal_status`, `stale_verdict`, and `current_regime` (contract §3.2):

| Position | When | Action |
|---|---|---|
| Section unwritten, eligible | `section_state[(cycle, section)] = unwritten` and all precondition stages complete (§7 map) | dispatch `scaffold-section`; the student then writes directly |
| Pending-scaffold handoff | `(cycle, section)` present in the `pending_scaffold` field (`project-walkthrough` recorded it on a `writing_handoff`) | dispatch `scaffold-section` for that section (the cross-track baton), then **clear** the entry |
| Section written, intact | `section_state = written`, no upstream revision | no dispatch — the student edits directly; `compile-working-paper` reports state on request |
| Section stale (gate firing) | `stale_verdict` non-empty, `current_regime = planning` | **hard gate** — re-scaffold the upstream-most stale entry via `scaffold-section`; the student rewrites directly; repeat until empty |
| Section sealed | `seal_status[cycle] = sealed` (whole cycle's block treated `sealed`) | **refuse** rewrite; redirect to the next cycle's superseding section (Moment R) |
| Compile view requested | student asks, or a skill calls it | dispatch/call `compile-working-paper` |

## Owning the dependency map and the hard stale-rewrite gate

The dependency/stale-detection map (architecture spec §7) operates **within a cycle, planning regime only.** This skill **owns the map and the gate** (architecture spec §12). The enforcement loop (contract §6):

1. **When the gate fires.** After any dispatch returns and on every entry: if `stale_verdict` is non-empty and `current_regime = planning`, the gate fires. It does **not** fire in execution regime — once a cycle is sealed, the planning-regime stale logic is replaced by cross-cycle supersession (§6.3).
2. **Populating the staleness set (the map lookup) — `project-walkthrough`'s, per critique F2.1.** When a planning-regime revision occurs, the §7 map is consulted to compute the downstream staleness set, written to `stale_verdict`. **Who performs the lookup:** the revising stage-skill is `project-walkthrough`-dispatched and returns `newly_stale_sections`; `project-walkthrough` does the §7 lookup at its integration and **populates `stale_verdict`** (the existing §5.2/§6.2/§10 + three-skill consensus). **This walkthrough owns the *gate*** — it reads `stale_verdict`, enforces, and **clears** each entry as its rewrite completes; `stale_verdict` is **shared** (project-walkthrough populates, this walkthrough clears). *(An earlier draft moved the lookup here to honor spec §12's "owns the dependency map"; critique F2.1 — blocking — showed that broke the most surfaces. "Owns the map" is read as **owns the gate/enforcement**; the only contract correction is §2.1's `stale_verdict` owner column → shared. Open item 1.)*
3. **Enforcing the gate.** While `stale_verdict` is non-empty: pick the **upstream-most** stale section (closest to the revision source), dispatch `scaffold-section` if a re-scaffold is needed (the existing scaffold may still apply), the student rewrites directly, and on the rewrite's `completed` return remove that section from `stale_verdict`. Repeat until empty. **The student cannot bypass the gate:** a request to move on while `stale_verdict` is non-empty gets the gate explanation (Moment G) and the re-offered dispatch, not progress.

## The named exceptions to the stale-rewrite rule (§9)

Three things **never** appear in `stale_verdict`, and the validator (contract §2.2 rule e) flags any state file that violates this:

1. **The abstract** — written once at the very end; a write-last summary, never stale-rewritten.
2. **Each cycle's Hypotheses / Expected Findings section** — frozen at that cycle's proposal approval; never rewritten (integrity). Superseded only by the next cycle's own new expected-findings section.
3. **All sections of a sealed cycle** — once sealed, every section in the cycle's block is frozen; later cycles supersede with new versions in their own blocks.

The §7 map is constructed to respect these exclusions, so the gate logic needs no extra special-casing — but this walkthrough's reads honor them, and it never re-scaffolds an excepted section.

## The all-sections writing model — dispatching `scaffold-section`

Every section's *writing* — foundational and structured — is this walkthrough's, through `scaffold-section` (architecture spec §5.4; contract §4.4). A section reaches its writing flow by a **handoff**:

- **Foundational sections** (research problem, research questions, Literature Review and Synthesis, gap, Hypotheses/Expected Findings): the project-mentor's foundational stage-skill finishes the section's *substance* and returns the **`writing_handoff` outcome** (contract §5.1); `project-walkthrough` records the pending-scaffold marker; this walkthrough reads it and dispatches `scaffold-section`. *(For `conduct-literature-review`, only the **synthesis layer** is handed off — the checklist summaries are written by the student during reading, in the research-agent flow, markerless; `scaffold-section`/`compile-working-paper` skip the already-written summary-run items.)*
- **Structured sections** (methods, results, discussion): written in execution regime; the **research agent** finishes the substance (the execution work) and initiates the same handoff (cross-agent-obligations **R10**); this walkthrough dispatches `scaffold-section` for the structured section (its dual-context dispatch carries `paper_structure_entry` + the Method Exemplar Articles).

**Dispatch preconditions (this walkthrough's gate, contract §4.4):** before dispatching `scaffold-section`, the walkthrough verifies all upstream cycle-template stages required for that section (§7 map) are complete, and that the section's cycle is not sealed (the seal gate). It is **authoritative** on these preconditions; `scaffold-section` trusts the dispatch and a precondition violation reaching it is a contract bug, not a runtime case.

**The unlock model (§5.4).** The full **foundational** scaffold roadmap is laid up front (the foundational skeletons are structurally fixed); the **structured** sections unlock whole-section-at-a-time once the method-approach and `paper_structure.md` are set. This walkthrough enforces the unlock order through the §7 precondition check.

## The re-surface vs. stale re-scaffold distinction (resolved)

`scaffold-section` is dispatched for two structural cases, and a third superficially-similar case is **not** this walkthrough's — the distinction is by **trigger source**, and getting it right keeps the single-writer discipline clean:

- **First-time writing handoff** → dispatch `scaffold-section` (a pending-scaffold marker exists, the section is `unwritten`).
- **Stale re-scaffold** → dispatch `scaffold-section` (a planning-regime revision rippled into an already-written section; the trigger is `stale_verdict` being non-empty). `scaffold-section` re-composes the affected section's scaffold; the student rewrites directly.
- **Dropped-marker re-surface — NOT this walkthrough's, NOT `scaffold-section`'s** (F-2 resolution (i), 2026-06-10). When the student deletes a single template marker without writing it, the re-lay is `compile-working-paper`'s, performed mechanically in-pass during its reconciliation (no `stale_verdict` entry, no scaffold dispatch). This walkthrough does **not** dispatch `scaffold-section` for a dropped marker — that case never reaches the stale gate. So: **this walkthrough dispatches `scaffold-section` only for a first-time handoff or a `stale_verdict`-driven re-scaffold; `compile-working-paper` owns the dropped-marker restore.**

## The seal — refusing sealed-cycle rewrites

This walkthrough **knows the seal logic** (§6) and enforces it on the paper side:

- **On the seal.** When `project-walkthrough` writes `seal_status[cycle] = sealed` (the §7 seal transition), this walkthrough, on its next read, updates every `section_state` entry for that cycle to `sealed`.
- **Refusing rewrites.** If the student attempts to rewrite a section in a sealed cycle, the walkthrough refuses the `scaffold-section` dispatch and runs §6.3's redirect: the way to express a changed view is the next cycle's own version of that section (Moment R), routed back through `project-walkthrough`'s new-cycle path if appropriate.
- **Execution-regime writes are new content, not rewriting** — the methods/results/discussion sections written during execution are always permitted (new content); the seal forbids rewriting the *planning* sections, not writing the execution ones.

## `compile-working-paper` — the callable utility

`compile-working-paper` is the thin mechanical state utility this walkthrough (and other skills) call — at startup, on request, or embedded in handoff prompts. It reconciles `working_paper.md` against the expected-item set (the `section-scaffolds.yaml` template + `paper_structure.md`), renders the living view (prose / open-skeleton / progress + page budget), runs the **paper-chain integrity check** (re-surfaces a dropped marker, flags a kept marker left in finished prose, restores a dropped/renamed heading from the template — all mechanical, never composing prose), owns the **Google-Docs round-trip reconciliation** (manual export or the OAuth pull), and writes the per-sync `versions/` snapshot. It reports **mechanical facts only** and makes **no judgment about the writing** (decision-history §8.2). This walkthrough calls it; it does not duplicate its mechanics.

## Coordination with `project-walkthrough` (state file only)

The two walkthroughs never call each other; all coordination is through `project_paper_status.md` (contract §10). This walkthrough's side:

- **Reads** the project-owned position fields (`current_cycle`, `current_regime`, `cycle_template_stage`, `seal_status`, `reference_articles_status`) read-only, and the **`pending_scaffold`** list `project-walkthrough` records on a `writing_handoff` — which this walkthrough reads and **clears** when it dispatches `scaffold-section` (the one cross-track field it writes-by-clearing; encoding resolved in the Cycle-1 sim, Finding 4).
- **Writes** `section_state` (per-cycle, per-section: `unwritten` / `written` / `stale` / `sealed` — its owned field) and **clears** entries in the two **shared** fields it does not populate: `stale_verdict` (project-walkthrough populates via the §7 lookup; this walkthrough clears each entry as its rewrite completes — F2.1) and `pending_scaffold` (project-walkthrough sets; this walkthrough clears on dispatching `scaffold-section` — F2.2).
- **The newly-stale handoff (§5.2).** `project-walkthrough` does the §7 lookup and **populates** `stale_verdict` (from the revising skill's `newly_stale_sections`); this walkthrough reads it, fires the gate, and **clears** each entry as its rewrite completes (shared field, F2.1).
- **Active-walkthrough switching.** A conversation may switch active walkthroughs mid-session as the student's intent shifts; the state file is the only continuity.

## The scripted moments

This skill is gating; its student-facing surface is the gate and the refusal. Per the convention, adapt wording to context; preserve the core and order; concise, no rationale lectures.

### Moment G — The stale-rewrite gate (a revision made a written section stale)

> *"Before we move on, one thing needs a refresh first. When you changed [the gap], it left [your gap section] out of step with your new thinking. Let's update that section now so the rest stays consistent. I'll help you adapt the scaffold for it, and then you'll rewrite it in your own words. Once it's caught up, we'll keep going."*

### Moment R — The sealed-cycle refusal (a rewrite aimed at a sealed cycle)

> *"That section belongs to a cycle we've already sealed — its proposal was approved, so it's the 'before' in your before-and-after, and rewriting it would erase that record. If your view has changed, that's real progress, and the place it goes is your next cycle: you'll write its own version there. Want to look at whether a new cycle is the right next step?"*

## What this skill writes to the workspace

`project_paper_status.md` only — its owned field `section_state`, plus the **shared** fields it clears (`stale_verdict` entries as the gate clears; `pending_scaffold` entries on scaffold dispatch) and the shared `last_updated` / `last_skill_dispatched` on its own dispatches. It does **not** populate `stale_verdict` (project-walkthrough does, via the §7 lookup — F2.1), write the project-owned position fields, or write `working_paper.md` (that is `scaffold-section` laying markers, `compile-working-paper` reconciling, and the student writing directly). It dispatches and gates; it produces no paper prose of its own.

## Edge cases

**A `stale_verdict` with multiple entries.** The gate works **upstream-most first** (closest to the revision source), because rewriting a downstream section against soon-to-change upstream content wastes the student's work. Each `completed` rewrite removes its entry; the gate re-evaluates until empty.

**A writing handoff arrives while a stale gate is open.** The stale gate has priority — a plan must be internally consistent before new sections are written on top of it. The pending-scaffold marker waits; the gate clears first.

**The student deleted a section heading (the reconciliation anchor).** Not a stale case and not a re-scaffold — `compile-working-paper`'s integrity check restores the heading from the template mechanically (never touching prose). This walkthrough does not dispatch `scaffold-section` for it.

**A structured-section handoff in execution regime.** Reached via the research agent's R10 handoff, not a foundational `writing_handoff`. This walkthrough dispatches `scaffold-section` in its structured context (`paper_structure_entry` + Method Exemplar Articles). The seal does **not** block this — execution sections are new content, permitted post-seal.

**The student asks to rewrite a frozen Hypotheses/Expected Findings section (planning, same cycle, pre-seal).** Refinement during planning is allowed (no data exists yet; §5.3) — the freeze attaches at *proposal approval*, not before. The walkthrough distinguishes "still in planning, refinable" from "sealed, frozen" by the cycle's `seal_status`; the §9 exception is about the *post-seal* frozen state.

## Where this skill lives in the architecture

A **keystone walkthrough** dispatched by the mentor at conversation start when the student's intent is paper-track (architecture spec §12), bundled in the project-mentor distribution (curator-immune, update-refreshed per platform primer §7). It reads the project-owned `project_paper_status.md` fields and writes `section_state` + `stale_verdict`; it owns the dependency/stale map and the hard stale-rewrite gate, the seal-refusal logic, and the all-sections scaffolding-and-writing dispatch (`scaffold-section`), and it calls the mechanical `compile-working-paper`. It coordinates with `project-walkthrough` through the state file alone. It is **not** a rationale trigger and **not** a model-escalation moment. Authoring it (with `project-walkthrough`) is the dispatch contract's promotion trigger (contract §12): once both are authored against the contract and a Cycle-1 end-to-end dispatch is simulated, the contract's substance promotes into architecture spec §12 and the contract retires.

## Status

**Draft, authored 2026-06-10 (Cowork)** against the architecture specification (§§5.1, 5.4, 6, 7, 9, 10, 12), the dispatch contract (§§2, 3.2, 4.4, 5, 6, 7, 10, 11), the decision-history (§§5.2, 8.1, 8.2), and the committed paper skills it dispatches/calls (`scaffold-section`, `compile-working-paper`). Paired with `project-walkthrough` (same session). Validated by the Cycle-1 dispatch simulation. **Teacher-admin critique applied 2026-06-10** (critique at `critiques/walkthroughs-critique-2026-06-10.md`; all findings accepted): **F2.1** (blocking) — reverted the `stale_verdict` resolution; this walkthrough now owns the **gate** (enforcement) and **clears** `stale_verdict` entries, while `project-walkthrough` populates via the §7 lookup (shared field); **F2.2** — `pending_scaffold` is an explicitly shared two-writer field (it clears); **F4.1** — Moment R kept parallel with `project-walkthrough`'s Moment S for the voice read. **Educator voice read complete 2026-06-10:** Moment G dropped the trailing "— and a plan should hold together" (design-rationale in student voice); Moment R's "the right move?" → "the right next step?" (the broadened "move"-as-a-noun voice ban). **Committed.**

### Open authoring items

**Convention** (per dispatch contract §11): items remain numbered while open; resolved items are retained with strikethrough, their original wording preserved, and a resolution date.

1. ~~**The `stale_verdict`-ownership seam — resolve toward paper-walkthrough doing the lookup.**~~ **Resolved per critique F2.1 (the opposite direction):** `project-walkthrough` does the §7 lookup and **populates** `stale_verdict` (the §5.2/§6.2/§10 + three-skill consensus, via `newly_stale_sections` — no new channel); this walkthrough owns the **gate** and **clears** entries; `stale_verdict` is **shared**. Spec §12's "owns the dependency map" = owns the gate/enforcement. The only contract correction: §2.1's `stale_verdict` owner column → shared (+ the spec §12 clarifying note). Tracked for the contract pass.
2. ~~**The pending-scaffold marker's concrete encoding.**~~ **Resolved (Cycle-1 sim Finding 4; critique F2.2):** a **`pending_scaffold`** list field keyed by `(cycle, section)`, chosen over a `section_state` value of `pending-scaffold` so the project track never writes the paper-owned `section_state`. It is a **shared** field — `project-walkthrough` sets entries; this walkthrough clears them on dispatching `scaffold-section` (two-writer, F2.2). Add `pending_scaffold` to contract §2.1 as shared (tracked for the contract pass).
3. **Stale re-scaffold dispatch recognition.** The draft fires `scaffold-section` for a stale re-scaffold from `stale_verdict` being non-empty. Whether `scaffold-section` needs a dispatch field distinguishing first-time-handoff from stale-re-scaffold (it re-composes either way, but may want to know) — confirm against the committed `scaffold-section` (its open item 4).
4. ~~**The Cycle-1 end-to-end simulation.**~~ **Done** (`cycle-1-dispatch-simulation-2026-06-10.md`): the flow is coherent end-to-end; surfaced refinements folded in, and the teacher-admin critique applied (see Status).
5. **Pre-seal completeness gate (critique F-9b) — `project-walkthrough`'s.** The convergence gate that reads `section_state` before the stage-14 seal dispatch lives in `project-walkthrough` (it dispatches `assemble-proposal`); this walkthrough's only role is owning `section_state`, which that gate reads. No change here; noted for cross-draft agreement.
