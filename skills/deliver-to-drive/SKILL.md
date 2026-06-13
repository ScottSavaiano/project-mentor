---
name: deliver-to-drive
description: The outbound Drive-writing utility — pushes section scaffolds into the student's living Google Doc and captures the per-session student-facing Paper Archive snapshot. The outbound counterpart to compile-working-paper (which is inbound). Called by scaffold-section (scaffold delivery) and at session open (archive snapshot). Never writes prose; never an escalation moment.
---

# deliver-to-drive

**Status:** First draft 2026-06-13 (Cowork). Authored against the dispatch contract (§4.6 grants, §5 return payload) and the architecture specification (§11 directories, §12 paper-skill inventory), resolving the outbound-delivery concern left open by `compile-working-paper` open items 3, 7, 8, and 9 (item 3 = the working-Doc id's home). Two educator rulings ground it (2026-06-13): the outbound Drive writes live in **one delivery utility** (not folded into two homes), and **automated delivery is the taught default** with the manual paste path as the degradation fallback. **Teacher-admin critique applied 2026-06-13** (0 blocking, 7 substantive + 4 minor, filter educator-approved — F4.2 session-open ordering pinned, F3.1 student-deletes-old-block step added, F6.1 fallback claim tightened, F7.1/F7.2 payload modes clarified, F2.2 recreation re-seed clarified, S1 headline; the compile-side residuals F5.2/F1.2/F5.1 land in the joint recommit). **Educator voice read complete 2026-06-13** — both held student-facing strings approved (the stale-re-scaffold revision heading; the fresh-Doc recreation note); **name `deliver-to-drive` ratified.** Committed with the recommitted `compile-working-paper` + the `scaffold-section` outbound-leg edit (joint commit).

## What this skill is

The **single outbound Drive-writing lobe.** `compile-working-paper` is the *inbound* reconciler (Google Doc → `working_paper.md`); this skill is its *outbound* counterpart (workspace → Drive). It is a **thin, purely mechanical utility** — not a student-facing conversation, **not a rationale trigger, not a model-escalation moment, default model tier.** It owns every write the curriculum makes *into* the student's Google Drive, so there is one place that holds the Workspace-call mechanics, the OAuth-vs-manual fallback, and the STS outbound boundary — not two.

It performs exactly two operations, invoked from two places:

1. **Scaffold delivery** — invoked by `scaffold-section` after it composes a section's scaffold into `working_paper.md`. Pushes that scaffold (function-prompt lines + attributed demonstration quotes) into the student's **living working Doc** so the student opens an already-seeded Doc rather than pasting a scaffold by hand.
2. **Paper Archive snapshot** — invoked at **session open**. Captures a coarse, per-session, dated, student-facing snapshot of the paper into the Drive **"Paper Archive"** folder (the clickable authentic-authorship trail; doubles as STS process evidence).

Both write only to Drive. The **canonical source of truth stays `working_paper.md`** in the git-backed workspace; Drive is the *writing surface* and the *browsable student-facing archive* (architecture: git backbone, Drive front-end, settled 2026-06-10). This skill never writes `working_paper.md`, never writes the walkthroughs' state file, and never composes or edits prose.

## The STS outbound boundary (load-bearing)

**Only scaffold *structure* is ever written outbound — function-prompt lines and demonstration quotes that are attributed direct quotations from the student's own chosen articles. Never AI-composed prose, never a draft sentence of the student's paper.** The archive snapshot is a copy of what the **student** wrote; this skill copies, it never authors. This is the same line `scaffold-section` and `compile-working-paper` hold, restated at the one place bytes leave the workspace for Drive.

## Operation 1 — Scaffold delivery

Invoked by `scaffold-section` as the outbound leg of a section's scaffold (the composition into `working_paper.md` is `scaffold-section`'s; the Drive write is this skill's — one lobe).

**First delivery of the project (no working Doc yet):**

1. Resolve or create the student's per-project Drive folder (e.g. `Research — <project short title>`) via `drive create-folder` (look up by stored id first; create only if absent).
2. `docs create --body` a working-paper Doc seeded with the section's scaffold; move it into the project folder.
3. Create the **`Paper Archive`** subfolder (id stored for Operation 2).
4. Persist the returned ids to **`drive_refs.yaml`** (this skill's own small state file): `working_doc_id`, `research_folder_id`, `paper_archive_folder_id`.

**Every subsequent section:** `docs append` the newly-unlocked section's scaffold to the **end** of the existing working Doc (id from `drive_refs.yaml`).

**Append is end-only — a known Docs-API constraint, designed around, not against:**
- Sections unlock in paper order as the cycle proceeds, so appending at the end keeps the Doc in reading order in the normal case.
- The Doc is the *writing surface*, not the source of truth. Any structural correction — a dropped-marker re-surface, a heading restore, a mid-document fix — happens in **`working_paper.md`** (compile-working-paper owns it) and is never retro-edited into the Doc body (the Workspace skill cannot edit an existing Doc body in place; it supports `create`, `append`, `get` only).
- **Stale re-scaffold** (an upstream planning-regime revision makes a written section's scaffold stale, and `scaffold-section` re-composes it): because the section cannot be edited in place *by the API*, this skill **appends the revised scaffold at the end** under a clear heading. The student, who *can* edit their own Doc even though the Workspace API cannot, is asked to delete the superseded block once they've reworked — so the Doc doesn't accumulate two visible versions of a section (the exact in-place-edit capability the API lacks but the student has; F3.1). Heading string (educator-approved 2026-06-13): *"Revised scaffold for <Section> — your earlier version is above. Rework it using this, then delete the old block above, and let me know when you're done."* **`compile-working-paper` reconciles correctly whether or not the student actually deletes the old block** — it reconciles by heading id against `working_paper.md` (canonical), so a lingering superseded block in the Doc is cosmetic, not a correctness problem. *(Append-at-end is the pragmatic answer to the API's in-place-edit limitation; the student-deletes-the-old-block step, added per critique F3.1, keeps the Doc clean without needing an API the skill doesn't have. Open item 1; exact wording at voice read.)*

## Operation 2 — Paper Archive snapshot (per session)

**Captured at session open, not session close.** The per-session student-facing snapshot is taken on the agent's first turn (a session-start behavior), comparing the current `working_paper.md` against the last snapshot recorded in `drive_refs.yaml`:

**Ordering at session open is load-bearing (F4.2).** Three things want the first turn: `project-briefing` (auto-invoked), `compile-working-paper`'s session-open **inbound** sync (its sync trigger 2 — pulls the student's latest Doc edits into `working_paper.md`), and this Operation 2. **The inbound sync MUST precede this archive capture**, so "current paper" here means *post-sync* — the snapshot reflects the writing the student just did in their last session, not a one-session-stale state. Where the snapshot says "a read-only clone of the current paper," "current" is defined as *after compile's session-open inbound sync has run*. (The exact session-open wiring — SOUL behavior vs. hook — is open item 2 / compile open item 9; this is the *sequencing* requirement on top of that, and resolves with it.)

- **Changed since the last session** → `docs create` a dated Doc (`Paper — YYYY-MM-DD`) in the `Paper Archive` folder, a read-only clone of the current paper; update `last_archive_snapshot_date` + `last_archive_content_hash` in `drive_refs.yaml`.
- **Unchanged** → skip silently (the content hash matches); no empty dated Docs accumulate.

**Why session-open, not session-close (resolves compile open item 9 / M-3):** an on-`session_end` hook is unreliable on the shipped build (a killed window never fires it; issue #38252 watch). Capturing at session *open* against the last-recorded hash makes the per-session archive depend on a guaranteed event (the next session starting) rather than a fragile one, so the authentic-authorship trail never silently gaps. An `on_session_end` shell hook may *also* run later as an optimization, but it is not load-bearing — the session-open capture is the guarantee.

**Cadence is fixed (per the versioning design, 2026-06-10):** this per-session student-facing archive is the *coarse* tier; the *fine-grained* per-sync `versions/` snapshot is `compile-working-paper`'s and is untouched here. Two tiers, two owners, one source of truth.

## The fallback path (OAuth not authorized)

Automated delivery is the taught default *because OAuth is provisioned cohort-wide at setup* (Internal consent, one shared client symlinked into each profile — verified 2026-06-10). When the bundled `google-workspace` skill is **not** authorized (an un-provisioned or portable setup), this skill degrades cleanly:

- **Scaffold delivery →** it returns the composed scaffold text to the calling agent to **display in chat for the student to paste** into a Doc they create themselves — the manual handoff `scaffold-section` and the `writing-and-saving-guide` already describe. No Drive write is attempted.
- **Paper Archive snapshot →** **omitted.** The OAuth-off student still has the `versions/` archive (compile's quiet per-sync git-backed dated `.md` files) for the **audit/rollback** trail (agent-side, and teacher-side via git). What they do *without* is the **student-facing** Drive surface specifically — the *clickable* dated Docs a student living in the desktop app + their Doc would actually browse, and the Drive folder a student/teacher/competition can open to watch the paper grow week by week (the STS authentic-authorship framing). That is acceptable for this rare fallback (OAuth is cohort-provisioned at setup): the **student-facing Drive archive is a Drive-only enhancement, not a hard dependency**, and standing up a second *local* student-facing copy would either breach `versions/`'s single-writer grant or require a whole new directory + §4.6 grant for an edge almost no student hits.

The student-facing two-path framing (automatic vs. manual) already lives in `writing-and-saving-guide.md`; this skill is the agent-side mechanism behind the "automatic" path.

## Interfaces and ownership

- **Called by `scaffold-section`** (Operation 1) at the end of its compose step, and **at session open** (Operation 2) — wired as a SOUL session-start behavior alongside `project-briefing`'s auto-invocation, or an equivalent first-turn behavior. *(Exact session-open wiring — SOUL behavior vs. shell hook — pinned at SOUL/provisioning authoring; open item 2.)*
- **Owns `drive_refs.yaml`** (sole writer; §4.6 grant requested) — `working_doc_id`, `research_folder_id`, `paper_archive_folder_id`, `last_archive_snapshot_date`, `last_archive_content_hash`. **Read by `compile-working-paper`** for the Path-A OAuth pull (the working Doc id it needs — this resolves compile open item 3: the Doc id's home is `drive_refs.yaml`, not `project_paper_status.md`, keeping the state file's two-writer discipline intact).
- **Never writes** `working_paper.md`, `project_paper_status.md`, `versions/`, or any section prose.
- **Return payload — two invocation modes, neither a classic walkthrough dispatch (F7.1).** This skill is never dispatched by a walkthrough; it is invoked two ways.
  - **(a) Scaffold-delivery mode** — a **sub-invocation by `scaffold-section`**. It returns to `scaffold-section`: `completion_status` (`completed` | `partial` on a Workspace error) and `workspace_writes` naming `drive_refs.yaml` if updated. `scaffold-section` decides whether to surface `drive_refs.yaml` in its *own* `workspace_writes` up to the walkthrough — it need not (it is a machine-handles file the walkthrough does not act on). The general "does a sub-invoked utility's writes bubble to the dispatcher?" question is **shared with `compile-working-paper`** (also "callable by the other skills"); it wants one consistent answer, flagged as a small forward item (open item 6), not solved by expanding the §5.1 contract here.
  - **(b) Session-open mode** — Operation 2 is a **non-dispatched SOUL session-start behavior**, not a dispatch at all, so the §5.1 skill→walkthrough payload contract **does not apply** — there is no walkthrough consuming a return. It simply does its capture (or no-op) and ends.
  - In whichever mode, it **sets none of** `seal_state_change`, `new_cycle_decision`, `newly_stale_sections`, `writing_handoff` (null), or `decisions_appended` (empty) — it is mechanical and downstream of composition, triggers no handoff, and appends no rationale.

## Edge cases

- **Workspace API error mid-delivery** (rate limit, transient 5xx): report the failure plainly, leave `working_paper.md` (canonical) untouched, return `partial`; the next invocation retries. Never leave `drive_refs.yaml` pointing at a half-created Doc — persist ids only after a confirmed create.
- **`drive_refs.yaml` present but the Doc was deleted by the student** (`docs get` 404 on the stored id): recreate the Doc and update the id. Note that this is **not** a single-section first-delivery seed — by mid-project `working_paper.md` holds the **student's accumulated reconciled prose** plus any standing scaffold markers, so the recreate is a **full-paper re-seed from canonical `working_paper.md`** (a different content shape than Operation 1's first-section seed). **This is intended and STS-clean:** the prose going outbound is the *student's own writing* (their words, copied back to their Doc), and the markers are attributed structure — no AI-composed prose. Note it to the student so they know a fresh Doc exists — string (educator-approved 2026-06-13): *"I couldn't find your working Doc, so I've made you a fresh one with everything you've written so far — here's the link."*
- **Token expired / revoked** (was authorized, now fails auth): fall to the manual path for that turn and surface a plain note that re-authorization is needed (provisioning concern, not a student task).
- **Out-of-order unlock** (a later section delivered before an earlier one): append-end places it after the earlier-delivered content; the Doc reads in delivery order, `working_paper.md` stays canonical, compile reconciles by heading id. Acceptable — the Doc is the surface, not the truth.
- **No-change at session open**: skip the archive Doc (hash match) — handled in Operation 2.

## Open items

**Convention** (dispatch contract §11): items remain numbered while open; resolved items are retained with strikethrough, original wording preserved, and a resolution date.

1. **Stale re-scaffold delivery into an append-only Doc.** The append-at-end-with-a-revision-heading behavior (Operation 1) is the pragmatic answer to the Docs API's lack of in-place body edit. **Resolved-in-design per critique F3.1:** the option set now includes a third path — the **student deletes the superseded block themselves** (they can edit the Doc even though the API can't), folded into the revision-heading string, which keeps the Doc clean. `compile-working-paper` reconciles by heading id against canonical `working_paper.md` whether or not the student deletes the old block. Delete-and-recreate-the-whole-Doc remains rejected (discards the student's in-Doc comments/history). ~~Still open: the exact student-facing heading wording, for the voice read.~~ **Resolved 2026-06-13 — heading wording educator-approved** (Operation 1). Item closed.
2. **Session-open wiring for Operation 2.** Whether the per-session archive snapshot is a SOUL session-start behavior (agent first turn, the assumed default) or an `on_session_start` shell hook that drives the Workspace call. The Workspace `docs create` needs the agent/gateway path (it is the bundled skill), which favors the SOUL-behavior form; pin at SOUL/provisioning authoring (compile open item 9's session-hook question, shared surface).
3. **`drive_refs.yaml` ships in the workspace template.** The template (v1.0.1) does not yet carry a `drive_refs.yaml` placeholder. Add an empty/with-header placeholder in a template point release so a fresh workspace has the file this skill writes and compile reads. (Small standing item; does not block this design.)
4. **Per-project folder naming + multi-project layout.** `Research — <project short title>` is the working name. Confirm the folder/Doc naming and whether later cycles (2/3) reuse the one working Doc + archive folder (assumed yes — one paper grows across cycles) or branch. Pin at voice read.
5. ~~**Moment/agent-facing wording.** ...the stale-re-scaffold revision heading (open item 1) and the recreate-after-deletion note (edge cases) are student-visible strings; voice-read them.~~ **Resolved 2026-06-13 — both strings educator-approved** (the revision heading in Operation 1; the fresh-Doc note in the edge cases). Item closed.
6. **Sub-invocation payload bubbling (shared with `compile-working-paper`).** When a utility is *sub-invoked* by another skill (this skill by `scaffold-section`; `compile-working-paper` by its sibling callers) rather than dispatched by a walkthrough, it is unstated whether the utility's `workspace_writes` surface up to the dispatcher or stop at the calling skill. Likely benign here (`drive_refs.yaml` is a machine handle the walkthrough doesn't act on), but it wants **one consistent answer across both utilities** — pin it when the walkthrough↔utility call convention is finalized, not by expanding the §5.1 contract for this one skill (critique F7.1).
