---
name: project-walkthrough
description: The Project-track keystone — owns the student's position in the project (which cycle, which project initiation sequence or cycle-template stage, which regime) and dispatches the project stage-skills in order. Reads project_paper_status.md into the position model, validates consistency, runs the rationale/seal/upstream precondition gates before each dispatch, integrates each skill's return payload (routing writing_handoff to paper-walkthrough via the pending-scaffold marker, acting on seal_state_change and new_cycle_decision deterministically), and owns the project initiation sequence, the co-determination exception, and the new-cycle transition with the three-cycle hard cap. Coordinates with paper-walkthrough only through the shared state file. Status — draft, awaiting teacher-admin critique and educator review.
---

# Project Walkthrough

**Last edited:** 2026-06-10 (Cowork — first draft, authored against the architecture specification (§§3, 4, 6, 7, 10, 12) and the dispatch contract (§§2, 3.1, 4, 5, 6, 7, 10, 11) as the project-track keystone, paired with `paper-walkthrough`; resolves four deferred §11 decisions with documented defaults (state-file format §11.1, resume granularity §11.3, follow-up-entry type §11.10, the upstream-reopening return value §11.11) and `project-briefing` open item 1, flagged for the critique pass)
*Editing convention: see `00-handoff.md` → "Editing conventions" for editor identifiers and revision-marker rules. This skill also carries a Status section at the bottom recording the draft-vs-promoted lifecycle; the two coexist.*

## What this skill is

`project-walkthrough` is one of the two **keystone** skills (architecture spec §12). It owns the student's **Project-track position** and is the orchestrator the mentor runs at the start of every conversation whose intent is project-track work. It does not itself do the high-judgment conversation — that lives in the stage-skills it dispatches (`design-project`, `develop-research-question`, `conduct-literature-review`, `identify-gap`, `select-paper-structure-reference-articles`, `derive-paper-structure`, `design-detailed-methodology`, `assemble-proposal`, `assess-new-cycle`, `begin-next-cycle`). Its job is **position, dispatch, and integration**: read the state file into a position model, validate it, run the precondition gates, dispatch the right stage-skill, integrate what comes back, and write the state file forward — never letting position logic leak into the stage-skills (dispatch contract §2.1, §3.3).

It coordinates with `paper-walkthrough` — the Paper-track keystone — **only through the shared state file `project_paper_status.md`** (architecture spec §10; contract §10). The two never call each other directly. When project-track work produces a paper-writing handoff or a downstream staleness, `project-walkthrough` records it in the state file and `paper-walkthrough` acts on it on its next read.

## When this skill fires

The mentor reads `project_paper_status.md` at the start of every conversation. **The active walkthrough is the one the student's intent matches** (contract §10): project-track activity (developing the question, the gap, the methodology; advancing a cycle stage; a new-cycle decision) routes here; paper-track activity (writing or revising a section, asking for a paper-state view) routes to `paper-walkthrough`. A conversation may switch active walkthroughs mid-session if the student's intent shifts; the state file carries continuity across the switch. On entry the active `project-walkthrough` parses the file, **validates consistency (§"State validation" below)**, derives position, and either dispatches the next stage-skill or resumes an in-flight one.

This skill is **not** a rationale trigger and **not** a model-escalation moment — it is pure orchestration. The high-judgment moments and their escalation/consultation gates belong to the stage-skills it dispatches; this skill *enforces* the gates (below), it does not *carry* them.

## The state file and its format

The position lives, and only lives, in `project_paper_status.md` (contract §2.1 — "the state file is the only place position lives"). **Format (resolving contract §11.1):** the file is a markdown document whose body is a **single fenced `yaml` block holding the §2.1 fields**, preceded by a one-line human header naming the file and warning that the YAML is authoritative and should be edited only with teacher-admin guidance (the §2.2 recovery path). The YAML block is chosen over structured-prose markdown (which parses non-deterministically) and over a bare `.yaml` file (which breaks the workspace's all-markdown, git-diff-friendly convention and the educator's at-a-glance readability): YAML round-trips cleanly for the idempotent field-scoped writes both walkthroughs make, parses deterministically into the position model on entry, and stays human-inspectable for the educator's hand-edit recovery. The walkthrough never stores a regenerated human-readable summary *in the file* (it would drift from the authoritative YAML); a human summary, when wanted, is `project-briefing`'s rendered output, not state.

The fields, their owners, and value sets are the contract §2.1 table; this skill writes the `project-walkthrough`-owned fields (`program_phase`, `current_project_id`, `current_cycle`, `current_regime`, `cycle_template_stage`, `spine_complete`, `seal_status`, `reference_articles_status`).

**Shared fields (written by both walkthroughs, like `last_skill_dispatched`).** Two coordination fields are genuinely two-writer and are treated as shared, not single-owned (per the critique, F2.1/F2.2):

- **`stale_verdict`** — `project-walkthrough` **populates** it (on a downstream-staling planning-regime revision, by doing the §7 dependency-map lookup at its integration of the revising skill's `newly_stale_sections` return); `paper-walkthrough` **clears** entries as it enforces the gate. *(This is the 5-surface contract consensus — §5.2/§6.2/§10 and the three committed stage-skills all have `project-walkthrough` populating the verdict; the only contract correction is the §2.1 owner column → shared, plus a spec §12 clarifying note that `paper-walkthrough` "owns the dependency map" in the sense of owning the **gate/enforcement** while the lookup-and-populate runs at this walkthrough's integration. Reverts this draft's earlier over-rotation toward paper-walkthrough doing the lookup — open item 2.)*
- **`pending_scaffold`** — `project-walkthrough` **sets** the `(cycle, section)` entry on a `writing_handoff`; `paper-walkthrough` **clears** it on dispatching `scaffold-section` (Cycle-1 sim Finding 4; shared, F2.2).

This skill is **read-only** on `section_state` (`paper-walkthrough`-owned) — which it reads for the pre-seal completeness gate (below).

## State validation (every read, before any dispatch)

Per contract §2.2, the walkthrough runs the consistency checks on every read and **refuses to dispatch until consistency is restored**:

- (a) `current_regime = execution` ⟹ `seal_status[current_cycle] = sealed`; (b) `current_regime = planning` ⟹ the current cycle's seal is `unsealed`.
- (c.i) `cycle_template_stage = null` whenever `spine_complete = false`; (c.ii) `cycle_template_stage = 3` only when `current_cycle = 1` (stage 3 is skipped in later cycles).
- (d) `stale_verdict` is empty for any section whose cycle is `sealed`; (e) the §9 named exceptions (abstract, frozen Hypotheses/Expected Findings, sealed-cycle sections) never appear in `stale_verdict`.
- The `seal_status` map has a slot for `current_cycle` (an absent slot for the active cycle is a hard failure; §2.1 initialization rule).

On any failure the walkthrough does not auto-recover (a bad auto-recovery is costlier than a paused conversation). It surfaces a specific, in-voice error naming the failed rule and the field carrying the inconsistency, and routes the student to bring the state file to their teacher (Moment V below). Recovery is the educator's, via the teacher-admin profile (a Tier 8 skill yet to be authored; until then, from the §2.1 field semantics).

## The position model — how state resolves to a dispatch

`project-walkthrough` derives its dispatchable position from `program_phase`, `spine_complete`, `current_cycle`, `current_regime`, and `cycle_template_stage` (contract §3.1). The resolution:

| Position | When | Next dispatch |
|---|---|---|
| First-time project initiation sequence entry | `year1-first-time`, `spine_complete = false`, `cycle_template_stage = null` | `design-project` (project initiation sequence portion) |
| Project initiation sequence in progress | `spine_complete = false`, mid-sequence | resume `design-project` |
| Project initiation sequence done → Cycle 1 stage 1 | `spine_complete = true`, `current_cycle = 1`, `cycle_template_stage = 1` | `design-project` (method-approach portion) — the **co-determination exception** (below) |
| Mid-cycle planning, stage N (4–14) | `current_regime = planning`, `cycle_template_stage = N` | the stage-N skill (mapping below) |
| Cycle just sealed | seal just set → `execution`, stage advances to 15 | the execution-regime skill |
| Mid-cycle execution, stage N (15–18) | `current_regime = execution`, `cycle_template_stage = N` | the stage-N skill (stages 15–18 are the **research agent's** lane; this walkthrough hands the student across — below) |
| Cycle complete; new-cycle transition | stage 18 complete → `current_regime = between-cycles` | `assess-new-cycle`, then the transition (below) |
| Cycle 2/3 once-only-skip entry | `current_cycle ∈ {2,3}`, `cycle_template_stage = 1` | `begin-next-cycle` (owns later-cycle stages 1–2), then the template at stage 4 |
| Junior-year restart | `year2-restart`, new `current_project_id` | `design-project` (full project initiation sequence, fresh project) |
| Senior-fall refinement | `year3-refinement` | `compile-working-paper`, `framing-for-competition`; only an emergent new cycle reopens cycle dispatch |
| Project closed | `current_regime = closed` | none — conclusion and abstract written (§4.4) |

### The cycle-template stage → skill mapping (the dispatch order)

The 18-stage cycle template (architecture spec §4.2) maps to stage-skills as follows. **The walkthrough dispatches the skill for the current `cycle_template_stage` and advances the stage on the skill's `completed` return.** Stages whose *paper output* is a section are completed via the cross-track `writing_handoff` (the skill returns it; this walkthrough routes it to `paper-walkthrough`; the stage advances when the section's writing flow is under way — see "Return integration").

| Stage(s) | Skill | Note |
|---|---|---|
| 1 (method-approach) | `design-project` (Cycle 1) / `begin-next-cycle` (later) | rationale trigger (stage 1) |
| 2 (Project Reference Articles consolidated) | `design-project` (Cycle 1) / `begin-next-cycle` (later) | |
| 3 (research-problem paragraph) | `design-project` → `writing_handoff` | **Cycle 1 only**; once-only/fixed-for-life |
| 4–6 (exploration; two research questions; their paragraphs) | `develop-research-question` | stage 6 → `writing_handoff` |
| 7 (Literature Review and Synthesis) | `conduct-literature-review` | summaries written during reading (research-agent flow); synthesis → `writing_handoff` |
| 8–9 (two gaps; gap section) | `identify-gap` | stage 8 rationale trigger + escalation; stage 9 → `writing_handoff` |
| 10 (Paper Structure Reference Articles) | `select-paper-structure-reference-articles` | writes `reference_articles_status` |
| 11 (paper structure) | `derive-paper-structure` | writes `paper_structure.md` |
| 12–13 (two-part methodology; Hypotheses/Expected Findings) | `design-detailed-methodology` | stage 12 rationale trigger + escalation; stage 13 → `writing_handoff` |
| 14 (proposal; consultation chain; sign-off) | `assemble-proposal` | rationale trigger; **sets `seal_state_change`** |
| — | **CYCLE SEAL** at proposal approval (§"The cycle seal") | planning → execution |
| 15 (IRB) | research agent (execution lane) | this walkthrough surfaces the cross-agent handoff |
| 16–18 (methods/results/discussion written) | research agent; sections via R10 `writing_handoff` → `paper-walkthrough` | |
| — | stage 18 done → `assess-new-cycle` → the new-cycle transition | |

## Precondition gates (run before every dispatch)

Three operationally distinct gates fire at dispatch time against different evidence (contract §4):

1. **The five-question rationale + teacher-consultation gate.** Before dispatching any skill whose stage is a **rationale trigger** (contract §4.2: `design-project` stage 1; `identify-gap` stage 8; `design-detailed-methodology` stage 12–13; `assemble-proposal` stage 14; `begin-next-cycle` via `assess-new-cycle`), the walkthrough reads `decisions.md` and verifies the relevant consultation entry is present; if absent, it refuses and surfaces the consultation requirement (Moment C). **Self-producing triggers** (the consultation is produced *inside* the dispatched skill's run — `identify-gap`, `design-detailed-methodology`, the stage-1 instance) are the §4.2 interim semantics: the pre-dispatch check is a **no-op** (there is no earlier same-stage consultation to verify), the gate is enforced at the skill's rationale-phase close, and verified §5.3-style on return — a return claiming the rationale completed with no matching `decisions.md` append is treated as `partial` and re-dispatched.
2. **The seal-logic gate.** Before dispatching any project-stage-skill against a cycle whose seal is set inconsistently with a planning-stage dispatch, refuse and surface the cross-cycle-supersession path (§6.3 / Moment S) — the way to revisit a sealed cycle's framing is to open the next cycle, never to edit the sealed one.
3. **The pre-seal completeness gate (the convergence gate — critique F-9b).** The two-track decoupling (the project track advances on a section's *substance* while its *writing* is pending — the orthogonal positions of Cycle-1 sim Finding 3) means the project track can reach stage 14 with a foundational section still `unwritten` or sitting in `pending_scaffold`. But `assemble-proposal` compiles **already-written** sections (architecture spec §8) and the seal is **irreversible** — a cycle must not seal with a scaffold-only section. So **before dispatching `assemble-proposal` (stage 14), this walkthrough reads `section_state` and verifies every one of the current cycle's foundational sections is `written`** (research problem — Cycle 1; research questions; Literature Review and Synthesis; gap; Hypotheses/Expected Findings); if any is `unwritten` / `pending_scaffold`, it refuses the stage-14 dispatch and routes the student to finish that writing first (Moment P), which is the paper track's `scaffold-section` flow. The convergence the decoupling assumes is thus made explicit at the one irreversible boundary.
4. **(Paper-skill upstream-completion gate — `paper-walkthrough`'s, not this skill's.)** Listed here only to mark the boundary: scaffolding preconditions are checked by `paper-walkthrough` before it dispatches `scaffold-section`. This walkthrough never dispatches paper-skills.

## Return-payload integration

On a dispatched skill's return, the walkthrough integrates the §5.1 payload and writes the state file forward (the §2.3 step-b write, clearing `last_skill_dispatched`):

- **`completion_status`** drives the basic flow (contract §5.1): `completed` → integrate `workspace_writes`' state changes, append the `decisions_appended` IDs' verification, derive the next position, advance the stage; `partial` / `blocked-on-consultation` / `aborted-by-student` → integrate any partial writes, preserve or clear `last_skill_dispatched` per the value, exit the loop; `blocked-on-precondition` (stage-skill) → re-run the precondition check, address the missing precondition, re-dispatch; **`blocked-on-upstream-reopening`** (the new value, below) → route to the named upstream rationale gate.
- **`writing_handoff`** (set by a foundational stage-skill when a section's substance is complete) → the walkthrough **sets** the named `(cycle, section)` in the shared **`pending_scaffold`** field (the §10 cross-track channel; `paper-walkthrough` reads it, dispatches `scaffold-section`, and **clears** the entry — shared field, F2.2), and advances `cycle_template_stage` past the substance stage. The two positions are **orthogonal** — the project track moves on while the section's *writing* is the paper track's (Cycle-1 sim Finding 3); the pre-seal completeness gate (above) is where the two reconverge before the irreversible seal. The walkthrough never picks the scaffold position; it routes the sanctioned decision-outcome.
- **`seal_state_change`** (only `assemble-proposal` sets it) → run the §7 seal transition (below).
- **`new_cycle_decision`** (only `assess-new-cycle` sets it) → `new-question-path-opened` dispatches `begin-next-cycle`; `findings-conclusive-for-this-project` transitions `current_regime = closed` and proceeds to conclusion + abstract (§4.4).
- **`newly_stale_sections`** (a planning-regime revision rippled downstream) → the walkthrough performs the **§7 dependency-map lookup** and **populates `stale_verdict`** with the downstream staleness set (contract §5.2/§6.2). `paper-walkthrough` reads the verdict, enforces the gate, and clears each entry as its rewrite completes — `stale_verdict` is shared (this walkthrough populates, `paper-walkthrough` clears). No separate handoff field is needed: `newly_stale_sections` (the revising skill's return) is the input, and the verdict is the output (§10).
- **`advisory_notes`** → logged to the session record (not state-bearing; not an inter-skill carrier, §5.1).

**The "next position" derivation (also `project-briefing`'s read-only mirror — resolves project-briefing open item 1).** After integration, the next position is derived deterministically, **blocker-first**: (1) if `stale_verdict` is non-empty and `current_regime = planning`, the next action is the paper track's stale gate (paper-walkthrough) — project-track dispatch waits; (2) else if a rationale/seal gate is unmet, that gate; (3) else the skill for the current `cycle_template_stage` per the mapping. `project-briefing` computes a *recommendation* from this same ordering read-only (it never dispatches); this walkthrough is the authoritative computer-and-dispatcher. The shared rule lives here so the two cannot diverge.

## The project initiation sequence and the co-determination exception

The project initiation sequence (architecture spec §4.1) runs once per project: discipline interest → topic → research-problem identification. `project-walkthrough` advances it stage-by-stage (each project initiation sequence stage's completion is a return-payload event), **except** the once-only-sequence stage 3 ↔ cycle-stage-1 boundary, which is the **one acknowledged exception to "stage-skills hold no private position state"** (contract §3.1). The research problem becomes real only against a method-approach that can address it, so `design-project` owns that co-determination conversation internally and returns both outcomes in one pass; the state file transitions `spine_complete: false, cycle_template_stage: null` → `spine_complete: true, cycle_template_stage: 1` **atomically** at integration. The walkthrough guarantees the atomicity at the integration write; the exception is scoped to this one conversation and does not generalize.

## The new-cycle transition (and the three-cycle hard cap)

After stage 18 completes (`current_regime = between-cycles`), the walkthrough dispatches **`assess-new-cycle`** (the rationale + content gate that decides whether a finding has opened a genuinely new question — never a way around an unwelcome result, decision-history §5.4). On its `new_cycle_decision` return:

- **`new-question-path-opened`** *and* fewer than three cycles have run → advance `current_cycle`, add its `seal_status` slot (`unsealed`), set `current_regime = planning`, `cycle_template_stage = 1`, and dispatch **`begin-next-cycle`** (which owns later-cycle stages 1–2 — method-approach with Method Exemplar Articles inherited, no menu re-teaching; Project Reference Articles consolidation; stage 3 skipped — then hands into the template at stage 4).
- **`findings-conclusive-for-this-project`**, or **three cycles already run** → `current_regime = closed`; proceed to the conclusion (spanning all cycles) and the abstract (written last). **The three-cycle cap is hard** (architecture spec §3.3): a third cycle's completion routes to close regardless of `new_cycle_decision`; the walkthrough never opens a fourth.

## Resumption (file-authoritative, skill-granular)

**Resolving contract §11.3:** resumption is **skill-granular and file-authoritative**; the contract does not grow a sub-stage field. On a fresh read, `last_skill_dispatched` non-null means the prior dispatch produced no return-integration write (the §2.1 cleared-on-return semantics) — the resumption case. The walkthrough re-dispatches that same skill with `prior_dispatch_return = null` (a fresh-entry signal), and **the skill places itself from the workspace files** (every stage-skill documents file-authoritative resumption and is idempotent on no-prior-state, contract §11 item 8). The walkthrough does not track a skill's internal sub-stage; if a skill ever proves it cannot self-resume cleanly, the contract grows a sub-stage field then — not pre-emptively (flagged for the critique). The pre-dispatch/return-integration write pair (§2.3) is what makes this safe: no meaningful state lives only in the conversation buffer. **`last_skill_dispatched` is cleared only on a *completing* integration** (`completed` or `aborted-by-student`) and **preserved** on `partial` and the `blocked-*` values — otherwise a paused skill would not be re-dispatched on the next conversation (Cycle-1 sim Finding 1; this tightens contract §2.1's "cleared on the return-payload integration write," which §5.1's `partial` handling already implicitly contradicts with "preserve `last_skill_dispatched`").

## The new return value: `blocked-on-upstream-reopening`

**Resolving contract §11.11.** When a stage-skill discovers mid-dispatch that it cannot proceed because an *upstream rationale-trigger* stage must be reopened (the canonical case: research-question development at stage 5 genuinely points at a method-approach change at stage 1), the existing §5.1 values fit poorly: `blocked-on-precondition`'s handling assumes the walkthrough can satisfy the precondition by re-running a check, but this case needs a **student + teacher decision to reopen a rationale-trigger stage**, which the walkthrough cannot satisfy on its own. This draft **formalizes a distinct `blocked-on-upstream-reopening` value**: the payload names the upstream stage to reopen; the walkthrough routes the student through that stage's **rationale + consultation gate** (it does not silently re-dispatch), and on the reopening's resolution re-derives position from the (now possibly changed) upstream decision. Distinct handling earns a distinct value (the contract's "the value carries the meaning" principle), rather than overloading `blocked-on-precondition` with a payload sub-field the walkthrough must inspect. **Consequence (tracked for the contract/skill pass):** contract §5.1 gains this value, and the three committed skills that adopted the interim `blocked-on-precondition`-with-explanation convention (`develop-research-question`, `identify-gap`, `design-detailed-methodology`) should be updated to return the new value at their method-approach-contradiction edge cases. Flagged for the critique and the next contract pass — not applied to those committed skills in this draft.

## Coordination with `paper-walkthrough` (state file only)

The two walkthroughs never call each other; all coordination is through `project_paper_status.md` (contract §10). The concrete channels this walkthrough uses:

- **The writing handoff.** On a `writing_handoff` return, this walkthrough **sets** the `(cycle, section)` in the shared `pending_scaffold`; `paper-walkthrough` reads it on its next activation, dispatches `scaffold-section`, and **clears** the entry. Field ownership: this walkthrough writes the project-owned position fields + `reference_articles_status`; `paper-walkthrough` writes `section_state`; the two **shared** fields are `pending_scaffold` (this walkthrough sets, `paper-walkthrough` clears) and `stale_verdict` (this walkthrough populates, `paper-walkthrough` clears).
- **The newly-stale handoff (§5.2).** When a stage-skill's return reports `newly_stale_sections`, this walkthrough does the §7 dependency-map lookup and **populates `stale_verdict`**; `paper-walkthrough` reads it, fires the gate, and clears each entry as its rewrite completes. The single contract correction is the §2.1 owner column for `stale_verdict` → **shared** (this walkthrough populates, `paper-walkthrough` clears), reconciling §2.1 with the §5.2/§6.2/§10 + three-skill consensus that has this walkthrough doing the lookup (open item 2).
- **The seal.** On `seal_state_change`, this walkthrough performs the §7 seal write order; `paper-walkthrough` updates the cycle's `section_state` to `sealed` on its next read.
- **Active-walkthrough switching.** A conversation may switch active walkthroughs mid-session as the student's intent shifts; the state file is the only continuity.

## The cycle seal (`seal_state_change` integration)

When `assemble-proposal` returns `seal_state_change` set, the transition is **eventually-atomic with consistency-validator recovery** (contract §7). The walkthrough validates that the full consultation chain and teacher sign-off are recorded in `decisions.md`, then writes, **in this order** (load-bearing for partial-completion recovery): (2a) `cycle_template_stage` 14 → 15, (2b) `current_regime = execution`, (2c) `seal_status[current_cycle] = sealed` **last** (it is irreversible from the contract's perspective; writing it last means a partial completion before 2c leaves the cycle still in planning, recoverable by redoing the proposal-return integration). `paper-walkthrough` sees the seal on its next read and freezes the cycle's sections. After the seal the student is in execution regime — the IRB / collection / analysis / interpretation work that is the **research agent's** lane; this walkthrough surfaces the cross-agent handoff (the concrete `research`-command pattern, the same pattern `conduct-literature-review` Moment 3 uses) and the execution-regime sections return via the research agent's R10 `writing_handoff`.

## The scripted moments

This skill is orchestration; most of its surface is silent state work. The student-facing moments are the **gates and the error surface** — where the walkthrough must speak. Per the convention, adapt wording to context; preserve the core and order; concise, no rationale lectures.

### Moment C — The consultation gate (a rationale trigger's consultation is missing)

> *"Before we lock this in, this is one of the decisions you and your research teacher and/or outside mentor decide together — it's too important to settle on our own. Talk it through with them, or send them a one-sentence summary of what you're deciding and why, and come back when you have. Then we'll continue."*

### Moment S — The sealed-cycle redirect (a revision aimed at a sealed cycle)

> *"That section is part of a cycle we've already sealed — its proposal was approved, so it's the 'before' in your project's before-and-after, and we don't rewrite it. If your thinking has changed, the way to show that is in your next cycle: you'll write its own version of this. Want to look at whether a new cycle is the right next step?"*

### Moment V — The state-file validation failure (consistency rule failed)

> *"Something in your project's status file doesn't line up — [the file says we're in Cycle 1, planning, but Cycle 1 is marked sealed]. I can't move us forward until that's sorted out, because I'd risk building on a wrong picture. Send your teacher the contents of `project_paper_status.md` and ask them to take a look; they can help you figure this out."*

### Moment X — The execution-regime cross-agent handoff (seal just set; stages 15–18 are the research agent's)

> *"Your proposal is approved — that's the cycle sealed, and a real milestone. From here the work shifts to actually running your study: IRB if you need it, collecting your data, running your analysis. That's your research agent's job, not mine. Open your research agent by clicking its icon on the bottom left of the app window, and paste this so it knows exactly where you are: [composed handoff prompt]. When that work is done and you've interpreted your results, it'll send you back to me."*

### Moment P — The pre-seal completeness gate (a foundational section isn't written yet)

> *"We're almost ready to put your proposal together — but first, [your gap section] still needs to be written; right now it's just a scaffold you developed, waiting for your words. Approving the proposal locks this cycle, so everything it's built on has to actually be written first. Let's finish that section, and then we'll assemble the proposal."*

## What this skill writes to the workspace

`project_paper_status.md` only — the `project-walkthrough`-owned fields (§2.1), the shared `last_updated`/`last_skill_dispatched`, the shared **`pending_scaffold`** (it sets entries; `paper-walkthrough` clears), and the shared **`stale_verdict`** (it **populates** via the §7 lookup on a downstream-staling revision; `paper-walkthrough` clears entries as it enforces). It **does not** write the six content files (the stage-skills write those) or `section_state` (paper-walkthrough's — it reads `section_state` for the pre-seal completeness gate). It dispatches skills; it does not produce paper prose or rationale entries of its own.

## Edge cases

**The student's intent is ambiguous between tracks.** The walkthrough that was active last stays active until the student's intent clearly shifts; a genuinely ambiguous turn is resolved by asking the student what they want to work on, not by guessing a dispatch.

**A `partial` return mid-rationale at a self-producing trigger.** The §4.2 interim semantics: the gate is verified at return, so a `partial` that claims the rationale but shows no `decisions.md` append is re-dispatched (the rationale is not yet real). No state advances past the trigger until the append is verified (§5.3).

**A method-approach contradiction surfaces downstream (stage 5/8/12).** The skill returns `blocked-on-upstream-reopening` naming stage 1; the walkthrough routes the student + teacher through stage 1's rationale gate before any downstream rework stands on the changed approach. A method-approach change is near-catastrophic for the cycle (§7) and the walkthrough treats it with that gravity.

**The third cycle completes.** The new-cycle transition routes to `closed` regardless of `assess-new-cycle`'s `new_cycle_decision` — the three-cycle cap is hard (§3.3). The mentor names the cap honestly if the student wants a fourth.

**A Year-2 restart.** `program_phase = year2-restart` with a fresh `current_project_id` re-enters the full project initiation sequence via `design-project`; the prior project's `decisions.md` is available as conversational context, not inherited verbatim (contract §9.3 default).

## Where this skill lives in the architecture

A **keystone walkthrough** dispatched by the mentor at conversation start when the student's intent is project-track (architecture spec §12), bundled in the project-mentor distribution (curator-protected, update-refreshed per platform primer §7). It reads `project_design.md` and `project_paper_status.md`; it writes the project-owned `project_paper_status.md` fields. It dispatches the ten project stage-skills and owns the project initiation sequence, the co-determination exception, the new-cycle transition, and the three-cycle cap. It coordinates with `paper-walkthrough` through the state file alone. It is **not** a rationale trigger and **not** a model-escalation moment — it enforces those gates on behalf of the skills it dispatches. Authoring it (with `paper-walkthrough`) is the dispatch contract's promotion trigger (contract §12): once both are authored against the contract and a Cycle-1 end-to-end dispatch is simulated, the contract's substance promotes into architecture spec §12 and the contract retires.

## Status

**Draft, authored 2026-06-10 (Cowork)** against the architecture specification (§§3, 4, 5.4, 6, 7, 9, 10, 12), the dispatch contract (§§2, 3.1, 3.3, 4, 5, 6, 7, 10, 11), the decision-history (§§4.3, 5.4), and the ten committed stage-skills it dispatches. Paired with `paper-walkthrough` (same session). Validated by the Cycle-1 dispatch simulation. **Teacher-admin critique applied 2026-06-10** (critique at `critiques/walkthroughs-critique-2026-06-10.md`; 1 blocking, 3 substantive, 1 minor — all accepted): **F2.1** (blocking) — reverted the `stale_verdict` resolution to the consensus (this walkthrough populates via the §7 lookup; `stale_verdict` shared, `paper-walkthrough` clears; no invented channel); **F2.2** — `pending_scaffold` made an explicitly shared two-writer field; **F-9b** — added the pre-seal completeness gate (stage-14 precondition + Moment P); **F1.1** — §11 item 7 carved out as an explicit cross-agent deferral for the promotion pass; **F4.1** — Moment P/S/V/X kept for the voice read (S/R parallel noted). **Educator voice read complete 2026-06-10:** Moment C gained "and/or outside mentor"; Moment S's "the right move?" → "the right next step?" (the **"move"-as-a-noun voice ban**, broadened from the 2026-06-07 "moves" ruling — decision-log); Moment V's "set it right" → "help you figure this out"; Moment P's "just a scaffold" → "just a scaffold you developed,"; Moment X reverted to platform-neutral ("Open your research agent…") pending the **desktop-as-primary** curriculum-wide revision (decided 2026-06-10, tracked). **Committed.**

### Open authoring items

**Convention** (per dispatch contract §11): items remain numbered while open; resolved items are retained with strikethrough, their original wording preserved, and a resolution date.

1. **State-file format default (§11.1) — confirm.** This draft picks a fenced `yaml` block in `project_paper_status.md` (machine-clean + educator-editable + git-native). Confirm at critique; the alternative (embedded structured-markdown) was rejected for non-deterministic parsing.
2. **The `stale_verdict` seam (§2.1 vs §5.2/§6.2) — resolved per critique F2.1.** An earlier draft moved the §7 lookup to `paper-walkthrough` to honor spec §12's "owns the map"; the critique (F2.1, blocking) showed that broke the most surfaces (an undefined PW→pW channel, §10 line 366, the three committed skills). **Reverted to the consensus:** `project-walkthrough` does the §7 lookup and **populates `stale_verdict`** (the existing §5.2/§6.2/§10 + three-skill design, via the `newly_stale_sections` return — no new channel); `paper-walkthrough` owns the **gate** (enforcement) and **clears** entries. `stale_verdict` is **shared**. Contract change shrinks to: §2.1 owner column for `stale_verdict` → shared; a spec §12 clarifying note that "owns the dependency map" = owns the gate/enforcement. Tracked for the contract pass.
3. **`blocked-on-upstream-reopening` (§11.11) — contract + committed-skill ripple.** This draft formalizes the new return value; contract §5.1 must gain it and the three committed skills' interim `blocked-on-precondition` usage updated. Tracked as a contract/skill-pass consequence, not applied here.
4. ~~**The writing-handoff marker's concrete shape in the state file.**~~ **Resolved (Cycle-1 sim Finding 4; critique F2.2):** a **`pending_scaffold`** list field keyed by `(cycle, section)`, chosen over a `section_state` value of `pending-scaffold` so the project track never writes the paper-owned `section_state`. It is a **shared** field (this walkthrough **sets** entries on a `writing_handoff`; `paper-walkthrough` **clears** them on dispatching `scaffold-section` — two-writer, like `last_skill_dispatched`, per F2.2). **Add `pending_scaffold` to contract §2.1 as a shared field** (tracked for the contract pass).
8. **Pre-seal completeness gate (critique F-9b) — added.** Before dispatching `assemble-proposal` (stage 14), this walkthrough reads `section_state` and refuses if any of the cycle's foundational sections is not `written` (Moment P) — the convergence point the orthogonal-positions decoupling needs before the irreversible seal. No contract change (it reads an existing field); confirm the foundational-section set at the contract pass.
9. **§11 item 7 (cross-agent execution-stage writes) stays open (critique F1.1).** Who advances `cycle_template_stage` through execution stages 15–18 is the research agent's, deferred to the Tier 2/3 build — promotion criterion (c) is not literally met for item 7. **Carve item 7 into spec §12 as an explicit cross-agent deferral** at the contract-promotion pass, rather than claim it resolved. (Item 2 — Year-2 restart inheritance — is resolved by `design-project`'s full-redo default.)
7. **`last_skill_dispatched` clear-on-completing (Cycle-1 sim Finding 1).** The draft clears `last_skill_dispatched` only on `completed`/`aborted-by-student` and preserves it on `partial`/`blocked-*`. Contract §2.1's "cleared on the return-payload integration write" should be tightened to "cleared on a completing/aborting integration" (it already contradicts §5.1's `partial` "preserve" wording). Tracked for the contract pass.
5. **Follow-up-entry type (§11.10) — default kept.** Standalone `decisions.md` entries referencing the original rationale by ID, no type taxonomy; the walkthrough verifies appends by the `decisions_appended` IDs only. Revisit only if follow-up cases proliferate.
6. **Resume granularity (§11.3) — default kept.** Skill-granular, file-authoritative; no sub-stage field. Revisit only if a skill proves it cannot self-resume.
