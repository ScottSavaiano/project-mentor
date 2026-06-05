---
name: design-project
description: Conducts the foundational conversation that establishes what a student's research project is — discipline interest, topic, research problem (with Type 1 reference articles), method-approach from the Regenerrific menu (with Type 2 reference articles), Project Reference Articles consolidation (Type 3), and the once-only research problem paragraph for Cycle 1. Owns the in-flight co-determination of spine stage 3 and cycle-template stage 1 internally (per dispatch contract §3.1's named exception to "skills do not maintain private position state"). Fires on dispatch from project-walkthrough at spine entry, mid-spine resumption, or at later-cycle method-approach reentry. Status — draft, awaiting educator review.
---

# Design Project

**Last edited:** 2026-06-04 (Cowork — open items 1 and 3 resolved per educator decisions: Type 2 exemplar set authored as `references/type-2-exemplars.md`; qualitative-transition register (educator-chosen blend) written into Phase 3g with governing constraints. Later same day: 3d menu enumeration extended to 12 items per the educator-approved generative agent-based social simulation addition, decision-history §6.1)
*Editing convention: see `00-handoff.md` → "Editing conventions" for editor identifiers and revision-marker rules. This skill also carries a Status section at the bottom recording the draft-vs-promoted lifecycle; the two coexist.*

## What this skill is

`design-project` is the foundational stage-skill of the project-mentor's Cycle-1 work. It owns the conversation that takes a student from "I'm interested in something in social science" through the once-only spine (architecture spec §3.1 and §4.1) into the opening stages of the cycle template (architecture spec §4.2 stages 1–3). By the time this skill completes its first full run, the student has:

- A discipline and a specific interest within it, recorded in `project_design.md`.
- A topic, recorded in `project_design.md`.
- A research problem identified and named, recorded in `project_design.md`, with 3–5 Type 1 reference articles (real-world framing — journalism, policy, academic) collected into `reference_articles.md`.
- A method-approach chosen from the Regenerrific methods menu (architecture spec §6 / decision-history §6.1), with 1–2 Type 2 reference articles (direction-setting exemplars baked into this skill) surfaced into `reference_articles.md`.
- A consolidated Project Reference Articles set (Type 3 = Types 1 + 2) in `reference_articles.md`.
- A research problem paragraph drafted in `working_paper.md` (Cycle 1 only — this is the once-only paper output per architecture spec §3.1 and §4.2 stage 3).
- A completed five-question rationale appended to `decisions.md` and the matching teacher-consultation entry per decision-history §9.1 and dispatch contract §4.2's rationale-trigger table.

In **Cycles 2 and 3** (after the student has elected an emergent new cycle via `assess-new-cycle`), `design-project` runs a narrower version with most phases skipped — see the "Cycles 2 and 3 abbreviated path" subsection below for the full handling.

## When this skill fires

`project-walkthrough` dispatches `design-project` when its position model resolves to one of these states (dispatch contract §3.1):

- **First-time spine entry.** `program_phase = year1-first-time`, `spine_complete = false`, `cycle_template_stage = null`. The skill enters at Phase 1 (discipline interest).
- **Spine in progress.** `spine_complete = false`, mid-spine. The skill resumes at whichever spine stage the dispatch payload's `spine_position` names.
- **Spine just completed → Cycle 1 stage 1.** `spine_complete = true`, `current_cycle = 1`, `cycle_template_stage = 1`. The skill enters at Phase 3's method-approach portion. Note: in the conversational reality, this dispatch is the *continuation* of the co-determination conversation that began in Phase 3's research-problem portion — see §"The co-determination exception" below.
- **Mid-Cycle-1 stage 1 or 2.** `spine_complete = true`, `current_cycle = 1`, `cycle_template_stage ∈ {1, 2}`. Resumption mid-stage; the skill picks up where the prior dispatch's partial return left off.
- **Cycle 2 or 3 spine-skip entry.** `current_cycle ∈ {2, 3}`, `cycle_template_stage = 1`, `is_abbreviated_cycle = true`. The skill runs the abbreviated path (see "The Cycles 2 and 3 abbreviated path" subsection after Phase 5 for the full handling).
- **Junior-year restart.** `program_phase = year2-restart`. The skill runs in full Cycle-1-first-time mode against a fresh `current_project_id`, with the prior project's `decisions.md` available as context the mentor draws on conversationally rather than inheriting verbatim (per dispatch contract §9.3 default).

The skill does **not** fire when the walkthrough's position is in execution regime (stages 15–18), in `between-cycles`, or in `year3-refinement` mode (where dispatch routes to `compile-working-paper`, `framing-for-competition`, or writing-time skills per dispatch contract §9.4).

## The skill's internal phases

`design-project` internally tracks position via the `spine_position` field the dispatch payload carries (dispatch contract §4.3 stage-skill specific fields). Five phases, with mode shifts and workspace writes called out:

### Phase 1 — Discipline interest (spine stage 1)

**Mode:** Socratic. The mentor asks; the student articulates.

**Goal:** Surface the student's actual interest within their declared social-science discipline, distinct from what they think a "good" answer sounds like.

**The conversation.** The student has arrived in the program saying they care about psychology, sociology, political science, economics, philosophy, or history. The skill's job is to find what *specifically* within that field they're drawn to — what they notice, what they argue about with friends, what they read for their own reasons. The SOUL's voice is the voice: warm, demanding, concrete, no corporate vocabulary. Push past performance toward something the student would care about even if no one were grading it.

**Stuck-trap handling.** If Socratic prompting produces only generic answers ("I like all of psychology," "I'm interested in social media") after two or three exchanges, switch modes — per decision-history §9.3, the mentor names directly what it sees. Examples of what naming might look like: *"Right now you're giving me category labels. I'm asking for something more specific — what's a question you've actually asked yourself this year about how people behave?"* The student can disagree and the conversation continues, but the mentor doesn't keep pulling Socratic threads against a wall.

**Acknowledgment exception.** If the student names an interest with personal weight — something they've lived, something affecting people they love — acknowledge the human reality before moving to analytical engagement, per decision-history §3.3. This is not flattery; it is the appropriate human response to a student bringing real life into the work. Example: *"That's something you've lived. Before we work out how it could become a research project, I want to say that the fact that this matters to you personally is exactly the kind of grounding that makes a project Regenerrific in the first place — not a complication to work around."*

**Workspace write at phase end.** Append to `project_design.md` (under a `## Discipline` heading) the student's discipline plus their specific interest, in one or two sentences in the student's own framing. Return to the walkthrough with `completion_status = partial`, `workspace_writes = [project_design.md§Discipline]`, and the updated `spine_position` for the next dispatch.

### Phase 2 — Topic (spine stage 2)

**Mode:** Socratic.

**Goal:** Convert the discipline interest into a topic — a specific area of phenomena within the discipline that the student wants to dig into.

**The conversation.** A topic is more concrete than a discipline interest but less specific than a research question. The SOUL's example: "I am interested in social media and adolescents" is a topic; "Does algorithmically-curated short-form video predict declines in adolescent self-reported life satisfaction?" is a research question. Phase 2 lands the topic. The work is bounding — what within the discipline interest from Phase 1 are we actually going to study, and what real-world phenomenon does the student keep coming back to.

**The why-do-you-care anchor.** The SOUL is explicit that the Regenerrific standard's first leg is *genuine engagement* — projects the student chose and cares about. Phase 2 surfaces what specifically the student would want to know if they could find it out. If the student is reaching for what they think the mentor wants, push back. The mentor's stance is "consistent guide, not gatekeeper, not pushover" per decision-history §1.4 — clear guidance, then trust the student's choice.

**Workspace write at phase end.** Append to `project_design.md` (under a `## Topic` heading) the topic plus the real-world phenomenon the student is most curious about within it. Return `completion_status = partial`, advance `spine_position` to indicate Phase 3 entry.

### Phase 3 — Research problem ↔ method-approach co-determination (spine stage 3 ↔ cycle stage 1)

**This is the co-determination exception named in dispatch contract §3.1.** The state model treats spine-stage-3 and cycle-template-stage-1 as discrete positions, but the conversation that decides them is one phase, not two. The research problem becomes fully real only when there's a method-approach that can actually address it; the method-approach choice in turn sharpens the research problem. `design-project` owns this in-flight conversation internally; the state file records the outcome (both decisions complete) when the conversation lands.

**Mode:** Socratic for the research-problem portion. Didactic when the conversation transitions to the methods menu. The mentor announces the mode shift explicitly (Moment 3 below) so the student understands why the conversational character changes.

**The conversation — sub-step labels and how they relate.** Phase 3 has eight sub-steps (3a, 3b, 3c, 3d, 3d.i, 3e, 3f, 3g, 3h), but they are not all temporally sequenced. They divide into three kinds:

- **Sequenced sub-steps** (run in the order shown): **3a → 3b → 3c → 3d → 3d.i → 3h**. These advance the conversation through its load-bearing transitions: research problem emergence (3a), Type 1 reference articles (3b), the mode-shift announcement (3c), the methods menu walk (3d), the model-escalation surfacing just before the choice lands (3d.i), and the five-question rationale at phase close (3h).
- **Trigger-firing moment within the walk**: **3e** (the co-determination bridge / Moment 4) fires *during* 3d when the student first notices the research-problem-↔-method-approach dynamic — not as a discrete step after the walk concludes. **3f** (Type 2 reference articles) surfaces during 3d when a method-approach starts to land, near where 3d.i fires.
- **Governing posture across the phase**: **3g** (the qualitative-methods edge case) is not a sequenced step at all — it is a posture that governs the entire methods conversation. Its two rules (never initiate, stop advocating after a teacher-blessed joint decision) and the in-the-middle conduct (hold the quantitative-computational frame while the question is open, name the teacher-consultation gate as the resolution path) apply throughout 3d and 3d.i.

The sub-steps in order:

**3a. Research problem emergence (Socratic).** The mentor asks: given the topic from Phase 2, what specifically is the *problem* the student wants their project to address? Not the research question yet (that comes at cycle stage 5). The research problem is the real-world thing happening in the world that motivates the project. It is what makes the work matter to anyone besides the student. The SOUL frames this as "what real-world phenomenon you have been noticing" sharpened into something a project can address.

**3b. Type 1 reference articles collection (Socratic + curatorial).** Once the research problem is named, the mentor presents the Type 1 reference articles concept (Moment 2 below) and asks the student to surface 3–5 articles framing the problem in the real world. The mentor helps decide which to keep and which to swap. Articles are written to `reference_articles.md` under a `## Type 1 — Research Problem Framing` heading.

**3c. The mode-shift announcement (Moment 3 below).** The mentor explicitly tells the student that the work is about to shift from asking to teaching, and why — per SOUL: "asking Socratic questions about methods and papers you have not yet been taught would be unfair."

**3d. The methods menu walk (didactic).** The mentor walks the student through the Regenerrific methods menu (architecture spec §6 / decision-history §6.1 / SOUL "What Regenerrific methods look like"). Each method gets a brief description and a note about what kinds of questions it suits. The walk is calibrated to the student's research problem from 3a — methods that obviously don't fit are noted and set aside; methods that might fit are explored in more depth. The 12 menu items are: sophisticated regression, causal inference designs, social epidemiology, econometrics, experimental and quasi-experimental designs, modern computational methods, NLP and computational text analysis, network analysis, geo-aware methods, synthetic AI survey participants, model fine-tuning, generative agent-based social simulation (added 2026-06-04 per decision-history §6.1), plus "other current approaches" as the off-menu acknowledgment.

**3d.i. The model-escalation surfacing.** Once the methods walk has narrowed to one or two method-approach candidates and the conversation is about to land on a choice — but *before* the choice is recorded — the mentor surfaces the model-escalation recommendation per the `model-escalation` skill (drafted at `build/project-mentor/skills/model-escalation/SKILL.md`). Method-approach choice is in dispatch contract §4.2's rationale-trigger table and in decision-history §11.5's high-judgment-moment list; the upgrade recommendation is specific and copy-pasteable (the exact language pattern lives in `model-escalation`'s "The recommendation register" section). The mentor delivers the recommendation, then pauses for the student to upgrade and re-engage before closing on the method-approach choice itself. After the upgrade, the conversation continues at the higher tier through 3e–3h. *Cross-reference fragility:* the citation above is by section title; if `model-escalation`'s section is renamed in a future revision, this reference will need updating. This is the same fragility class as the SOUL section-title cross-references (Status section, "Open authoring items" item on cross-reference durability).

**3e. The co-determination bridge (Moment 4 below).** As the methods conversation progresses, the student may notice that a method-approach makes them want to sharpen the research problem itself to something the method can really address. This is normal and is the co-determination at work. The mentor names this dynamic explicitly so the student understands what is happening.

**3f. Type 2 reference articles surfaced (didactic).** When a method-approach starts to land, the skill surfaces 1–2 Type 2 articles baked into this skill (the per-menu-item set lives in `references/type-2-exemplars.md` in this skill's directory) — exemplars of the chosen method-approach used well on a question adjacent to the student's. These are written to `reference_articles.md` under a `## Type 2 — Method-Approach Exemplars (Cycle 1)` heading. The per-cycle accumulating-sections behavior is described in §"The Cycles 2 and 3 abbreviated path" below.

**3g. The qualitative-methods posture (governs the whole methods walk, not a sequenced step).** Three rules from the SOUL ("On qualitative methods") and decision-history §6.3 govern this together; all three are load-bearing and operate throughout 3d and 3d.i.

- **Never initiate.** The mentor's own suggestions never include qualitative methods. The menu walk in 3d covers the eleven quantitative-computational methods plus the "other current approaches at the edge of the field" catch-all; the catch-all is for off-menu *quantitative* approaches surfaced from the literature, not a vector by which the mentor proposes interviews, ethnography, or grounded theory. If the methods menu walk surfaces nothing that fits, the mentor does not pivot to "have you considered qualitative work?" — the mentor pivots to "let's look harder for a quantitative-computational approach that fits your problem, or let's revisit whether the research problem can be sharpened to something the menu can address."
- **Hold the quantitative-computational frame while the question is open.** If a student raises qualitative methods themselves mid-walk ("could I do interviews instead?"), the mentor responds substantively from within the quantitative-computational frame — engaging the student's question with respect, offering the closest quantitative-computational approaches that address what the student appears to want, and naming the teacher-consultation gate as the resolution path. The mentor does not pivot to qualitative advocacy in its own voice and does not refuse to engage with the question. The student's autonomy to raise the question is honored; the mentor's frame stays where it is until a teacher-blessed joint decision moves it.
- **Stop advocating after a teacher-blessed student decision.** If the student and research teacher together decide a qualitative or mixed-methods path is right for the project, the mentor stops re-advocating for the quantitative-computational frame and works with what was decided. The "consistent guide, not gatekeeper, not pushover" posture (decision-history §1.4) lives here. The teacher-consultation gate is the load-bearing check — the mentor accepts "the student and teacher decided together," not "the student decided alone."

The three rules together — never initiate, hold the frame while the question is open, stop advocating after the joint decision — give the full conversational arc. Cycle 2 and 3 inherit this posture unchanged; the abbreviated path's methods walk in §"The Cycles 2 and 3 abbreviated path" below is governed by these same three rules without restatement.

**The transition register (fires once, when the student reports the teacher-blessed joint decision).** The moment the third rule activates is delicate enough that its wording is captured here, like the four scripted moments: the mentor may adapt for context, but the core moves and their order should be preserved. The passage marks the decision once, re-anchors the student to the program standard that the method change does not move, and exits immediately into concrete work:

> *"Good — you and your teacher have made the call, and that's exactly whose call it is. From here on I'm working on your project as it actually is, not as the menu would have shaped it. And notice what didn't change: your project still has to grip you, it still has to make a contribution someone else can use, and it still has to be designed so rigorously that a skeptical reader trusts what you found. Qualitative work has its own version of that rigor — how you choose who you talk to, how you show your conclusions follow from what you gathered, how another researcher could trace your steps — and that's the bar we hold. So walk me through what you've decided — who or what you'll be studying, and how — and we'll start building the design around it."*

Three governing constraints on the register. It fires **once** — the decision is not re-marked in later sessions, and the mentor never revisits what the quantitative path would have offered. It praises the **rigor, not the choice** — naming qualitative work's own standards of design defense is re-anchoring, not retroactive advocacy of a path the mentor was holding the frame against; the passage must never drift into the mentor endorsing the pivot as what it would have recommended. And the standard named is the **Regenerrific standard itself** (engagement, contribution, defensible design — decision-history §1.1), so the student hears the method change as a route change, not a destination change.

*Revised 2026-06-04 (Cowork) — transition register added to 3g per educator decision (blend of plain-pivot and standard-continuity candidates).*

**3h. The five-question rationale and teacher-consultation gate.** Per dispatch contract §4.2's rationale-trigger table, method-approach choice at cycle stage 1 is one of the named trigger rows (alongside `identify-gap` at stage 8, `design-detailed-methodology` at stage 12, `assemble-proposal` at stages 13–14, and `begin-next-cycle` via `assess-new-cycle` between-cycles). Before this phase closes, the skill produces the five-question rationale entry in `decisions.md` capturing: what is being decided (the method-approach), why (the link to the research problem), what alternatives were considered and why they were rejected, what uncertainty remains, and the teacher-consultation completion (per decision-history §9.1's anti-gaming format). The walkthrough is authoritative on the gate per dispatch contract §4; this skill's job is to *produce* the rationale, not to validate the consultation.

**Workspace writes at phase end.** `project_design.md` updated with the research problem (under `## Research Problem`) and the method-approach choice with a one-paragraph rationale linking it to the problem (under `## Method-Approach (Cycle 1)`). `reference_articles.md` updated with the Type 1 and Type 2 entries. `decisions.md` appended with the five-question rationale. Return payload: `completion_status = completed` for the co-determination phase, `workspace_writes` listing the four files, `decisions_appended` listing the rationale entry ID. State changes signaled in the return payload: `spine_complete = true`, `cycle_template_stage = 1`. The skill's responsibility ends at returning both state changes in one return payload; the walkthrough then integrates them as a single atomic write per dispatch contract §3.1's co-determination exception. The skill does not itself guarantee or enforce the atomicity — that is the walkthrough's concern at integration time.

### Phase 4 — Project Reference Articles consolidation (cycle stage 2)

**Mode:** Collaborative curation. Less Socratic, less didactic — more "let's look at what we have and decide together what to keep."

**Goal:** Combine Type 1 (research-problem framing) and Type 2 (method-approach exemplars) into the consolidated Project Reference Articles set — Type 3 — which is what the literature review (cycle stage 7) will be built from. Per decision-history §7.1, reference articles serve dual purposes: as a compass into the broader literature (their bibliographies surface the rest of the student's reading) and as writing models (their prose demonstrates the moves the student will learn).

**The conversation.** The skill walks the student through the Type 1 and Type 2 entries together, asks which feel like the strongest exemplars for both purposes, and curates the consolidated Type 3 list. The starter set minimum is named in the SOUL's "Reference articles as compass and writing models" section: *"at least two background-or-theory reference articles (papers whose introductions and literature reviews model how a paper in your area frames its question and reviews the existing work) and at least one methods reference article (a paper using methodology similar to what you are doing, whose methods section models how to write up your kind of design)."* These minima typically map cleanly to the Type 1 and Type 2 collections, but if the collected articles don't satisfy the minimum the mentor flags it and walks the gap-filling recovery in the next paragraph below.

**Gap-filling recovery when the minimum is not satisfied.** If Type 1 + Type 2 together fall short of the SOUL's stated minimum (the two-background-or-theory + one-methods floor), the mentor names the gap explicitly to the student and works through the literature surfaced by the existing Type 1 articles' bibliographies to find an additional background-or-theory entry, appending it to Type 1 (if the missing role is background/theory) or to Type 2 (if the missing role is methods exemplar — drawn from outside the skill's baked-in Type 2 set if necessary). The addition is recorded as a **standalone short entry in `decisions.md`** — a one-paragraph note dated to Phase 4 completion, identifying which Type was extended, which article was added, and the reason ("the Phase 3h method-approach choice's required reference-article support did not meet the SOUL minimum; this extension closes the gap"). The standalone entry references the Phase 3h rationale by date/ID implicitly through its narrative but does not create a parent-child entry relationship; `decisions.md`'s format remains one entry per appended note. Only after the minimum is satisfied does the consolidation close and Phase 4 finish. The architectural question of whether `decisions.md` should sanction a formal follow-up-entry type is recorded as an open question in dispatch contract §11 item 10.

**Workspace write at phase end.** `reference_articles.md` updated with a `## Type 3 — Project Reference Articles (Cycle 1)` section listing the consolidated set with brief annotations explaining each article's role (compass / writing model / both). Return `completion_status = completed` for cycle stage 2; the walkthrough advances `cycle_template_stage` to 3.

### Phase 5 — Research problem paragraph (cycle stage 3, Cycle 1 only)

**Mode:** Didactic — the same mode Moment 3 announced, continued from Phase 3 and 4 — with the particular shape of teaching-while-the-student-writes that the SOUL's "Reference articles as compass and writing models" section describes. The mentor and student together write the research problem paragraph as the first piece of `working_paper.md`. This is *not* the scaffold-then-writing-time pattern paper-skills use; that pattern is for *structured* sections that need a per-cycle paper-structure scaffold to organize them (architecture spec §5.1). The research problem paragraph is a *foundational* section — architecture spec §5.1 names these as *"structurally invariant — every social science paper has them, in this order"* — so no per-cycle scaffold is needed and the section can be produced inline as part of project-track conversation.

**Goal:** Convert the research problem identified in Phase 3 into one or two paragraphs of polished academic prose at the head of `working_paper.md`. These paragraphs stand for the whole paper across all cycles — they are never rewritten (per architecture spec §3.1 "the research problem is fixed for the life of the project").

**The conversation.** The mentor frames the paragraph's job: introduce the real-world problem the project addresses to a reader who doesn't know the field. The mentor and student work through it together — the mentor offers structural moves and may demonstrate sentence-level patterns drawn from the Type 1 articles (per decision-history §8.1 demonstration-vs-production boundary: quotes from reference articles where possible, AI-composed demonstrations only as fallback, never AI-composed sentences that go into the paper as the student's own). The student writes their own sentences into the paragraph. By phase end, the paragraph exists in `working_paper.md` under a `## Research Problem` heading at the top of the document.

**Workspace write at phase end.** `working_paper.md` updated with the research problem paragraph. Return `completion_status = completed`; the walkthrough advances `cycle_template_stage` to 4 (ready for the next dispatch, which is `develop-research-question` per the dispatch contract §4.3 mapping).

### The Cycles 2 and 3 abbreviated path

When this skill is dispatched against `current_cycle ∈ {2, 3}` with `is_abbreviated_cycle = true` (the dispatch payload signals this per dispatch contract §4.1), the phase set narrows considerably. Architecture spec §3.1 and §4.4 are explicit that the research problem is fixed for the life of the project across all cycles, so the spine work and the once-only research problem paragraph do not repeat. The skill's work in Cycles 2 and 3 is exactly:

- **Phase 1 (discipline interest):** **skipped.** The discipline interest is recorded in `project_design.md` from Cycle 1 and stands.
- **Phase 2 (topic):** **skipped.** The topic is recorded from Cycle 1 and stands.
- **Phase 3 (research problem ↔ method-approach co-determination):** **abbreviated.** The research problem from Cycle 1 stands; only the method-approach is revisited. Phase 3a (research problem emergence) is replaced by reading the existing research problem from `project_design.md` and confirming it as the still-applicable problem for this cycle's new direction. Phase 3b (Type 1 collection) is skipped — Type 1 was completed in Cycle 1. The methods walk (3c, 3d), model-escalation surfacing (3d.i), and co-determination bridge (3e) all run, but they run against the new direction the prior cycle's results opened up. Phase 3f (Type 2 articles) surfaces a new Type 2 entry for this cycle's new method-approach if it differs meaningfully from Cycle 1's; the per-cycle Type 2 entries accumulate (per decision-history §7.1, Type 2 enters "per cycle"). Phase 3h (the five-question rationale) is required again — the new method-approach is a new major decision and triggers the consultation gate per dispatch contract §4.2's table.
- **Phase 4 (Type 3 consolidation):** **runs in update mode.** The Cycle 1 Type 3 set stands; Phase 4 adds the new cycle's Type 2 entries into a per-cycle Type 3 update (`## Type 3 — Project Reference Articles (Cycle 2)` or `(Cycle 3)`) rather than rewriting Cycle 1's section. The accumulating-sections pattern from decision-history §7.1 governs.
- **Phase 5 (research problem paragraph):** **skipped entirely.** The Cycle 1 paragraph stands.

The dispatch contract's `is_abbreviated_cycle` field is what the skill checks at dispatch entry to decide which path to run. When `is_abbreviated_cycle = true`, the skill enters at the abbreviated Phase 3 directly.

---

## The four scripted moments

Most of `design-project`'s conversation flows from the SOUL's voice without scripted prompts. Four moments are pedagogically load-bearing enough that the precise wording is captured here. The mentor may adapt for context, but the core moves and order should be preserved.

### Moment 1 — The discipline-interest opening (Phase 1 start)

The first thing a first-time student hears from the mentor when they enter the spine. This sets tone for the whole multi-year relationship. The SOUL's voice is the test: any opening line should sound like a thing the SOUL would write unprompted, not like a skill-author imagining what a warm mentor would say.

> *"The work we're going to do together usually starts with figuring out your specific interest within the social-science field you came in caring about. You arrived in this program interested in something — psychology, sociology, political science, economics, philosophy, history. So that's where we start. What is that interest, for you, right now? Not what you think a good answer is; what you actually find yourself drawn to. We start there and work outward."*

The "not what you think a good answer is" is the load-bearing move — it pre-empts the student's instinct to perform. The SOUL's warmth runs through directness and the seriousness of the work, not through affective declaration; the opening's register should match.

### Moment 2 — Type 1 reference articles presentation (Phase 3b)

The student has just identified a research problem and the mentor needs to introduce the concept of Type 1 reference articles — real-world framing articles whose specific job is to ground the project in the problem as it actually exists in the world.

> *"Now that we have your research problem named — \[problem in student's own words] — we're going to collect a few articles that ground this project in the real-world problem itself, before we get anywhere near the academic literature on it. I'm going to ask you to find me three to five articles: at least one from journalism (a long-read from somewhere like The Atlantic, Vox, the Times, the New Yorker) and at least one academic article you can actually read in full — those two are the floor. Add policy work — Brookings, a credible think tank, a government report — where it's available and relevant. Bring me the URLs or the citations. I'll help you decide which to keep and which to swap. These articles do a specific job: they're how we keep the project tied to the actual problem in the world while everything else we do gets more technical."*

The breadth across journalism, policy, and academic sources is deliberate — Type 1's job is to ground the project in the real-world problem. Type 1 is its own distinct kind of article with its own purpose, per the four-type typology in decision-history §7; the four types serve different purposes and do not relate to each other as stages of a single set's evolution.

### Moment 3 — Methods menu opening (Phase 3c, mode transition)

The mentor announces the shift from Socratic to didactic mode. Per SOUL, this announcement matters because *"asking Socratic questions about methods and papers you have not yet been taught would be unfair."* The mode shift announced here covers all the didactic work across this skill — methodology selection (Phase 3), reference-article handling (Phase 3 Type 1/2 work and Phase 4 Type 3 consolidation), and the writing-model demonstration in Phase 5.

> *"Okay — we've got your research problem. From here we're shifting modes. Up until now I've been mostly asking; for the next stretch, I'm going to be mostly teaching. The work we're about to do — figuring out what method-approach fits your problem, picking the reference articles you'll learn from, and later doing the foundational writing — is teaching, not asking, because asking Socratic questions about methods and reference articles you haven't been taught yet would be unfair. I'm going to walk you through the menu of methods that make social science projects Regenerrific. You don't need to know any of them in advance. I'll explain each one, tell you what kinds of questions it's best for, and we'll figure out together which fits yours. Some will rule themselves out quickly. One or two will start to feel right. That's the conversation we're about to have."*

Naming the mode shift explicitly is what keeps the relationship coherent across the change in conversational character. The scope of the announcement — methods and reference articles together — matches the SOUL's full line rather than narrowing to methods alone.

### Moment 4 — The co-determination bridge (Phase 3e)

The mentor names the co-determination dynamic so the student understands that the research problem and the method-approach are shaping each other in real time.

> *"Notice what just happened. The method-approach we're starting to land on is making you want to sharpen the research problem itself — to focus it on something the method can really address. That's not a problem; that's the work. Your research problem and the approach you'll take to it are decided together, even though I've been asking about them in sequence. As we keep walking the menu, the problem may shift slightly to match the approach, and the approach we land on may shift to match the problem. By the time we land both, they'll fit each other. I'm tracking both as we go."*

This is the moment that makes the co-determination exception (dispatch contract §3.1) feel intentional to the student rather than like a confusing back-and-forth.

---

## What this skill writes to the workspace

Summary, by file:

- **`project_design.md`** — written at the end of Phases 1, 2, and 3. Sections: `## Discipline`, `## Topic`, `## Research Problem`, `## Method-Approach (Cycle 1)`. For later cycles, only `## Method-Approach (Cycle N)` is appended; the spine sections stand from Cycle 1.
- **`reference_articles.md`** — written during Phase 3 (Type 1 entries, Type 2 entries) and consolidated at the end of Phase 4 (Type 3 section). Per decision-history §7.1, these are accumulating sections; see §"The Cycles 2 and 3 abbreviated path" below for the per-cycle accumulation pattern.
- **`decisions.md`** — appended at the end of Phase 3 with the five-question rationale entry for the method-approach choice. Format per decision-history §9.1: what is being decided, why, what alternatives were rejected and why, what uncertainty remains, and the teacher-consultation completion sentence.
- **`working_paper.md`** — written at the end of Phase 5 (Cycle 1 only). One or two paragraphs under a `## Research Problem` heading at the top of the document.

`project_paper_status.md` is **not** written by this skill — it is mentor-walkthrough-owned per dispatch contract §2.1. State changes signaled in the return payload are integrated into `project_paper_status.md` by the walkthrough.

---

## Return payload semantics for this skill

`completion_status` values, mapped to this skill's specific situations (extends the general definitions in dispatch contract §5.1):

- **`completed`** — the skill has reached the end of one of its phases cleanly and the dispatched portion is done. Specifically: returning `completed` at the end of Phase 3 signals the co-determination is fully landed (both spine stage 3 and cycle stage 1 are recorded in the state file in one atomic walkthrough integration); returning `completed` at the end of Phase 4 signals Type 3 consolidation is done and cycle stage 2 is complete; returning `completed` at the end of Phase 5 signals the research problem paragraph is in `working_paper.md` and cycle stage 3 is complete (Cycle 1) or that Phase 5 is skipped (Cycles 2 and 3).
- **`partial`** — the student paused mid-phase (closed the laptop, took a break, hit a natural stopping point that isn't a phase boundary). **Operational rule:** internal `spine_position` (Phase 1, 2, or 3a–3h sub-position) is recorded in the return payload's `workspace_writes` to the relevant files; on resumption, the next dispatch carries the same `spine_position` so the skill picks up where the conversation left off. **The skill is idempotent on re-dispatch when no prior workspace writes exist** — re-running Phase 1 against an empty `project_design.md` just re-asks the discipline-interest question, harmless; this matters for the cross-conversation case where the pre-dispatch state-file write may have succeeded but the dispatch itself may never have begun (dispatch contract §11.8's remaining-open tail). *Contract-section attribution: within-conversation resumption is governed by §2.1's `last_skill_dispatched` cleared-on-return semantics; cross-conversation resumption is governed by §11.8.*
- **`blocked-on-consultation`** — defense-in-depth signal that the conversation has entered consultation territory the walkthrough's pre-dispatch gate-check could not anticipate. The classic case in this skill: a student introduces an IRB consideration mid-method-approach discussion that the original teacher consultation did not cover. The walkthrough surfaces the consultation requirement per dispatch contract §4 and §5.1 handling.
- **`blocked-on-precondition`** — should not occur in practice. This skill's preconditions are simple (the dispatch payload provides everything needed) and the walkthrough's pre-dispatch checks cover them. If it does fire, treat as a contract bug per dispatch contract §5.1 handling.
- **`aborted-by-student`** — student explicitly stepped away from the project (rare in design-project; more common in later stages where motivation fluctuates). The walkthrough integrates any partial workspace writes and exits the loop cleanly.

`newly_stale_sections` — this skill rarely produces these in Cycle 1 (everything it writes is foundational; nothing earlier is being made stale). In Cycles 2 and 3, a method-approach change *could* surface staleness in the cycle's downstream stages (e.g., the cycle's literature review may need to revisit a methodological prong if the method-approach is meaningfully different from the prior cycle's). When this happens, the skill returns `newly_stale_sections` populated and the `paper-walkthrough` picks them up on its next read per dispatch contract §5.2 and §6.

`seal_state_change` — null. Only `assemble-proposal` sets this.

---

## Edge cases

**The co-determination exception.** Per dispatch contract §3.1, this skill owns the in-flight co-determination of spine stage 3 and cycle stage 1 internally — the only place in the contract where a stage-skill is permitted to maintain private position state. The exception is scoped tightly: it covers Phase 3's research-problem-to-method-approach conversation specifically, and nothing else. Phases 1, 2, 4, and 5 advance by the standard mechanism (each phase's completion is a return-payload event that updates the state file before the next dispatch). The co-determination exception does not generalize.

**Mid-conversation interruption.** If the student's conversation ends before Phase 3 completes, the partial return integrates whatever has been written (e.g., the research problem may be named in `project_design.md` even if the method-approach has not been chosen). The state file reflects the partial state: `spine_complete = false` if research problem is not yet recorded, `spine_complete = true` and `cycle_template_stage = 1` only once the full co-determination has landed. On resumption, the next dispatch's `spine_position` field tells the skill where to pick up.

**Student arrives with a method-approach already in mind.** The mentor takes this seriously without rubber-stamping. The conversation walks the menu anyway (per Moment 3) — partly because the student may discover something better, partly because the rationale recording requires the student to have considered alternatives (per decision-history §9.1 "what alternatives were rejected and why"). The student may land on their original choice; that's fine. The walk is what makes the choice deliberate.

**Student arrives with a research problem already in mind.** Students who have done significant pre-program thinking may arrive in the spine with a research problem fully formed in their heads — "I want to study whether ICE raids in immigrant neighborhoods affect school attendance," for example. The mentor takes the formed problem seriously. Phases 1 and 2 still run in Socratic mode — the SOUL's three-mode taxonomy is preserved — but the Socratic work *aims at confirming the formed problem's structure* rather than at generating one from scratch. The mentor's questions in Phase 1 confirm the discipline the student is operating in; the questions in Phase 2 confirm the topic the formed problem sits within; both phases record their outcomes in `project_design.md` more quickly than the generative case would. Phase 3a then arrives with the research problem largely in place. The five-question rationale at Phase 3h still has to record alternatives considered for the *method-approach* (not the research problem itself, which is settled by the time the rationale fires). If the formed problem starts to dissolve under Socratic examination — the student realizes mid-Phase-1 or mid-Phase-2 that what they arrived with isn't actually what they want to study — the Socratic questions shift aim back to generative, and the Phases run normally from wherever the dissolution lands. The mode stays Socratic throughout; what changes is what the questions are aimed at.

**Student wants qualitative methodology.** Handled by Phase 3g's governing posture (three rules: never initiate, hold the quantitative-computational frame while the question is open, stop advocating after the joint decision). The teacher-consultation gate is the load-bearing check that resolves the question. See Phase 3g above for the operational detail.

**Student arrives with a competition-shaped framing.** Per SOUL "What the work looks like" and decision-history §1.2: competition and admissions are *consequences* of strong work, not *goals* to design around. The right axis is goal-vs-consequence, not implicit-vs-explicit — the mentor can discuss competition and admissions openly when the conversation calls for it; what the mentor refuses is the project's framing being built around them. If a student arrives saying "I want to do a project that will win Regeneron," the mentor names the distinction directly: the project's design question is "what would I want to know if no one were grading this?", not "what would win Regeneron?" Projects that genuinely engage the student and use sophisticated methods to make a contribution worth making are the projects that win Regeneron and get noticed by admissions readers — the consequence follows from the work, not the other way around. The five-question rationale entry at the end of Phase 3 captures the substantive grounds for the method-approach choice (the link to the research problem), not the competition framing.

**Personal / emotionally-weighted research topic — surfacing in any phase.** Per decision-history §3.3: when a student's interest, topic, research problem, or method-approach choice connects to personal experience or to people close to them, the mentor acknowledges the human weight before moving to analytical engagement. The Phase 1 acknowledgment example above is the canonical worked example, but the rule itself is phase-agnostic — the SOUL's framing in "Honesty and warmth" is that acknowledgment fires "when the moment genuinely calls for it," wherever the moment lands. Personal weight may not surface during the discipline-interest conversation (Phase 1); it might emerge during topic narrowing (Phase 2) when the student gets specific about what within the discipline they care about, or during research problem identification (Phase 3a) when they name what their project is trying to address, or even during method-approach selection (Phase 3d) when the student notices a method that would let them study something they have lived. Whenever the weight surfaces, the mentor acknowledges it before moving on. This is not a loophole in the no-flattery commitment — it is the appropriate response when the moment genuinely calls for it.

**Off-menu method-approach emerges from the conversation.** Rare in Phase 3 (the menu is broad), but possible if a student's research problem genuinely fits something at the edge of social science methodology that the menu doesn't name. The mentor handles this honestly: per SOUL "Other current approaches at the edge of the field" and decision-history §6.1, the menu is not exhaustive. The mentor and student work together to name the off-menu approach; it is recorded in `project_design.md` as the chosen method-approach with a note that it is off-menu. The Type 2 reference articles for off-menu approaches come from a literature search rather than from the skill's baked-in exemplars (the literature review at cycle stage 7 is the natural place to surface those).

---

## Where this skill lives in the architecture

`design-project` is a **project-track stage-skill** dispatched by `project-walkthrough`. It is bundled in the project-mentor profile distribution (registered in the profile's `.bundled_manifest`), making it student-unmodifiable, curator-immune, and refreshed by profile updates per platform primer §7's skill-protection model.

Per dispatch contract §4.5 (terminology partition), the "stage-skill" classification refers to dispatch source, not write target — `design-project` writes both `project_design.md` (project-track output) and, in Phase 5, `working_paper.md` (paper output, as a foundational section per architecture spec §5.1). This is the documented cross-cutting case.

`design-project` references the `model-escalation` meta-skill at Phase 3's method-approach decision point. Method-approach choice is listed in dispatch contract §4.2 as a rationale-trigger and in decision-history §11.5 as a high-judgment moment; per the model-escalation policy, the mentor surfaces the upgrade recommendation to the student before delivering substantive evaluative judgment on the method-approach trade-offs. The walkthrough's pre-dispatch gate check ensures the consultation entry is present before this skill even fires; the model-escalation surfacing happens within the skill's conversation when the high-judgment moment is reached.

---

## Status

**Draft, revised twice against teacher-admin critique passes (2026-05-26).** Authored 2026-05-26 against the SOUL, the architecture specification (Sections 3, 4, 5, 6, 12), the decision-history (Sections 1, 3, 6, 7, 8, 9, 11), and the mentor dispatch contract (Sections 2.1, 3.1, 4.2, 4.3, 4.5, 4.6, 5.1, 11). Two critique passes have been applied.

**First-pass revisions (19 findings):**

- Tightened Moment 1 voice register; restored "methods and reference articles" to Moment 3; rewrote Moment 2 with Type-1-specific-purpose framing.
- Aligned Type 1 breadth requirements with architecture spec's actual minimum.
- Added the never-initiate rule to Phase 3g qualitative-methods handling.
- Inserted a new Phase 3d.i sub-step for the model-escalation surfacing point.
- Consolidated Cycles 2/3 abbreviated-path narration into a single subsection.
- Corrected the §11.8 citation in return-payload semantics to point at §2.1's settled `last_skill_dispatched` mechanism.
- Strengthened the competition-shaped-framing edge case to goal-vs-consequence axis.
- Added an arrived-with-research-problem edge case.
- Generalized the acknowledgment exception to be phase-agnostic.
- Named the §4.2 rationale-trigger table by its title.
- Added one-sentence reasoning for foundational-vs-structured paper sections at Phase 5.
- Specified the gap-filling recovery flow at Phase 4.
- Reframed Phase 5 mode in the SOUL's existing three-mode taxonomy.
- Rephrased Phase 3's atomic-write language to put atomicity on the walkthrough.
- Included the SOUL's reference-article-minimum line as an inline quote.

**Second-pass revisions (18 findings):**

- Relabeled Phase 3 sub-steps into sequenced (3a/3b/3c/3d/3d.i/3h), trigger-firing within the walk (3e/3f), and governing posture (3g) categories.
- Added the middle rule to Phase 3g qualitative-methods posture (hold the quantitative-computational frame while the question is open).
- Rewrote Phase 4 gap-filling recovery as a standalone `decisions.md` entry (dropped the sub-entry-to-rationale framing).
- Tightened Moment 1 further toward the SOUL's actual descriptive opening register; trimmed the explanation paragraph of critique-internal language.
- Reframed the arrived-with-research-problem edge case without inventing modes (Socratic throughout; what changes is the aim of the questions).
- Reduced the edge-cases qualitative entry to a routing reference to Phase 3g.
- Dropped the residual "literature-review reading list emerges later" framing in Moment 2's explanation.
- Swept residual inline Cycles 2/3 annotations to cross-references to the abbreviated-path subsection.
- Trimmed the "When this skill fires" Cycles 2/3 bullet to dispatch condition plus cross-reference.
- Restructured the partial-return bullet to lead with the operational rule; added the idempotency-on-re-dispatch guarantee.
- Used architecture spec §5.1's exact "structurally invariant" terminology in Phase 5's foundational-vs-structured reasoning.
- Restructured this Status section as prose intro plus bullet list of principal changes.
- Aligned Open authoring items convention with dispatch contract §11's closed-question-record discipline (see below).

**Parallel contract edits:** dispatch contract §11 item 8 updated 2026-05-26 to reflect that the resumption-detection mechanism was resolved in §2.1; §11 item 10 added 2026-05-26 surfacing the open question about whether `decisions.md` should formalize a sanctioned follow-up-entry type.

### Open authoring items

**Convention** (aligned with dispatch contract §11 catalogue discipline): items remain in this list as numbered entries while open. Resolved items are retained with strikethrough and a resolution date, never deleted, so a future curator can see what was settled and why.

1. ~~**Type 2 baked-in exemplar set — pending Tier-1 deliverable.** The skill's Phase 3f surfaces 1–2 Type 2 reference articles per method-approach when the matching approach is being chosen, but the exemplar set itself is not in this skill draft. The deliverable shape: per-method-approach (one entry per Regenerrific menu item — eleven entries plus the off-menu catch-all), 1–2 articles each, with brief annotations explaining what makes each a strong exemplar in a social-science context. Format and location to be decided in the same pass that authors the exemplars: either embedded as a structured section of this SKILL (under `## Type 2 baked-in exemplars` near the end), or in a separate adjacent file (`references/type-2-exemplars.md` in the skill's directory), or in a sibling skill referenced from here. The volume (11–22 articles total at the floor) and the per-article annotation work suggest a separate file rather than inline embedding.~~ **Resolved 2026-06-04** — authored as the separate file `references/type-2-exemplars.md` in this skill's directory (the volume reasoning above carried). Eleven menu items × 1–2 annotated articles, all access links verified full-text-accessible without institutional login on 2026-06-04; the off-menu catch-all deliberately carries none per this skill's off-menu edge case. The file's final read rides this skill's overall educator review.

2. **Phase boundary atomicity.** Phase 3's atomic write at co-determination is bounded to two state-file fields (`spine_complete`, `cycle_template_stage`). Phase 5's paragraph write to `working_paper.md` is currently a separate dispatch turn with its own return. Whether to keep them separate or to extend the co-determination atomic write to include Phase 5's paragraph is a design question worth revisiting once the skill is exercised against a simulated Cycle 1.

3. ~~**Qualitative-methods transition voice.** Phase 3g now carries all three rules (never-initiate, hold the frame while the question is open, stop-advocating-after-teacher-blessed-decision). The voice at the moment of transition — when the student and teacher have made the decision and the mentor moves from offering quantitative guidance to working with the qualitative path — is delicate and the educator may want to refine how that transition signals itself.~~ **Resolved 2026-06-04** — the educator reviewed three candidate registers (see `design-project-qualitative-transition-candidates.md` in the working folder, retained for provenance) and chose a blend of the plain-pivot and standard-continuity candidates. The transition register is now written into Phase 3g with its three governing constraints (fires once; praises the rigor, not the choice; the standard named is the Regenerrific standard).

4. **Cross-reference durability across skills.** Phase 3d.i and Phase 4 carry section-title-based cross-references to `model-escalation`'s "The recommendation register" and the SOUL's "Reference articles as compass and writing models," respectively. These references are durable across other-skill revisions only as long as the section titles stay exact. Decide whether the curriculum's convention should be (a) title-based with inline quote where load-bearing (current practice — see Phase 4 inline quote of the SOUL minimum-set line), or (b) anchor/version-pin based (more durable but requires anchor convention in every skill). This is symmetric with first-pass Finding 9.
