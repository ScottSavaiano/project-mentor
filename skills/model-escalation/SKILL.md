---
name: model-escalation
description: Recognize when a mentor conversation has entered a genuine high-judgment moment, and surface a specific, copy-pasteable recommendation that the student upgrade from the default tier (Haiku 4.5) to the careful tier (Opus 4.8) for that question. Fires at proposal review, methodology framing at load-bearing decision points, gap identification, new-cycle assessment, the scaffold no-blueprint case, integrity questions touching the demonstration-versus-production boundary, and substantive critical feedback on a submittable draft. Does not fire on routine clarifying questions, status check-ins, or follow-ups after the student has already upgraded. A meta-skill — the high-stakes mentor skills reference this one rather than re-implementing the policy.
---

# Model Escalation

**Last edited:** 2026-05-26 (educator — refinements to default-tier identifier (Haiku 4.5) and downstream language; original Cowork draft 2026-05-22)
*Editing convention: see `00-handoff.md` → "Editing conventions" for editor identifiers and revision-marker rules.*

## What this skill is

This skill is a **meta-policy**. It does not handle a research stage on its own. It carries the curriculum's rule for **when to recommend a model upgrade** to the student, and it carries the language the mentor uses to deliver that recommendation.

Other mentor skills — the small set that handle genuine high-judgment moments — reference this skill instead of carrying their own escalation logic. "When do we suggest upgrading, and how do we say it" is therefore editable in one place rather than scattered across the skill library.

The reasoning behind the curriculum's model strategy — why Haiku 4.5 is the default tier, why Opus 4.8 is the deliberate upgrade, why the act of switching is itself pedagogically valuable — lives in `decision-history-and-rationale.md` Section 11. This skill operationalizes that strategy. It does **not** re-explain it to the student. The operating mechanics of the `/model` slash command and the dashboard model picker live in `hermes-platform-primer.md` Section 8.

## When this skill fires

Recognition is **situational**, not keyword-based. The question is what the student is *asking the mentor to do*, not what words appear in their message. A student asking *"is this a good research gap?"* is in a gap-identification moment; a student asking *"what does 'research gap' mean?"* is not.

Recognition happens **at the boundary of a new turn — before the mentor begins composing its substantive response**. If the mentor has already started generating an answer before noticing the moment qualifies, it finishes the thought it was forming (rather than cutting off mid-sentence) and then delivers the recommendation on the next turn. The skill should not interrupt partial reasoning; it should govern the *opening* of a high-judgment exchange, not its middle.

This skill fires at a small, deliberate list of moments:

- **Proposal review.** A formal evaluation of a research proposal against the Regenerrific standard. This is the highest-stakes mentor act and the clearest case for the upgrade.
- **Methodology framing at a load-bearing decision point.** When the student is choosing between methodological approaches at a point the dispatch contract or implementation plan flags as significant — not every methodology question, but the ones where the choice substantially shapes the rest of the project.
- **Gap identification.** When the student is evaluating whether their research question is genuinely novel, or whether the gap they have identified is real. This requires literature-aware possibility-space reasoning — the kind where the stronger model's edge is most felt.
- **New-cycle assessment.** When the student is weighing, between cycles, whether their completed cycle's results have opened a genuinely new question worth a next cycle. This is gap-identification reasoning applied to results — the same literature-aware, possibility-space judgment, now held against the report-then-decide firewall (a new cycle is earned by a new question, never by an unwelcome or expected result). It is high-stakes: it decides whether the project continues or closes. *(Added 2026-06-08.)*
- **Scaffold blueprint for a novel approach (the no-blueprint case).** When `scaffold-section` finds that neither a template nor any of the student's chosen articles can model a section — a previously-unapplied method, or a method-as-topic with a new wrinkle — and must judge which published precedent could serve as a structural blueprint (and whether a paper the student finds genuinely fits). Literature-aware possibility-space reasoning, bound up with the student's distinctive contribution, and the moment most exposed to confabulating a non-existent article — so the careful tier earns its keep both for the judgment and as a hallucination-risk-reduction layer (the structural guarantee is fetch-before-use; this is defense-in-depth). Narrow: routine scaffolding is *not* a moment. *(Added 2026-06-08.)*
- **Integrity questions touching the demonstration-versus-production boundary.** When the conversation is navigating the boundary between what the student must demonstrate themselves and what the agent may produce on their behalf. The decision-history's reasoning on this (Section 8) is precise; the mentor needs to reason at that level of precision.
- **Substantive critical feedback on a draft to be submitted.** When the student is asking for the mentor's evaluation of work they intend to submit (to a competition, to a teacher, to an external reviewer), the feedback needs to be at the highest level the mentor can deliver.

## When this skill does NOT fire

The principle: **do not flag at every opportunity.** If every other conversation contains an "upgrade now?" prompt, students will stop reading them, and the policy will have failed at its own purpose.

Specifically, this skill does not fire when:

- The student is asking a clarifying question about a concept ("what does *X* mean?", "can you remind me what *Y* is?"). These are explanatory, not high-judgment.
- The student is in routine scaffolding mode — working through `develop-research-question` exploration, building a literature-discovery seed set, organizing reference articles. The work is real, but it is not the kind of judgment-load that justifies the upgrade.
- The student has **already upgraded** for the current question and is following up. The skill recognizes that the upgrade has happened and lets the conversation proceed at the higher tier. Re-flagging would be nagging.
- The student is asking a question that is *adjacent* to one of the listed moments but is not itself the moment. ("How do I know if my gap is real?" — that is the moment. "Where in the paper does the gap section go?" — that is structural; not the moment.)

If the mentor is uncertain whether a moment qualifies, the default is **not** to flag. False positives erode the policy; false negatives merely mean the student does the work on the default tier, which is the explicit baseline.

## What this skill does NOT do

- **Automatically switch models.** Hermes does not support cascading auto-switches in this way, and this is by design. The skill recommends; the student executes. The deliberate act of switching is itself the pedagogical move.
- **Report token costs.** That is `/usage`'s job, taught in Lesson 1. This skill does not surface or recommend cost tracking. For budget visibility, the `check-budget` skill is the appropriate path.
- **Prompt the student to downgrade after the moment concludes.** The reference card from the setup lessons covers `/model default`; the student's habit, once formed, will handle it. A downgrade prompt would itself be a form of nagging.
- **Generalize to the research or review agents.** This skill is initially project-mentor specific. The research and review agents have their own escalation considerations (probably narrower, since their work is less judgment-heavy), to be addressed in their own design phase.

## What this skill does

When the mentor recognizes one of the listed high-judgment moments, it pauses its substantive response and delivers an upgrade recommendation. The recommendation contains three things:

1. **A specific, situational statement of what the student is asking and why it benefits from stronger reasoning.** Not generic ("this is a hard question") but tied to the actual moment ("you are asking me to evaluate the soundness of your causal inference").
2. **The exact command the student should type**, in the form taught in Lesson 1. The curriculum's stable form is the alias `/model careful` (which resolves through the profile's `model_aliases:` block to the current Opus identifier), typed into the desktop app's **Cmd+K command palette** — the **deliberate path the curriculum teaches** (the status-bar model picker also switches tiers with a click, but the typed command is the taught form, because choosing the careful tier *deliberately* is the pedagogical point; educator ruling 2026-06-13). The `/model` command preserves the conversation we are in — the student can switch and then simply tell the mentor to proceed; they should not need to re-ask their original question.
3. **A short note that the student can return to the default after this work concludes** — `/model default` — so the student does not silently keep Opus running for routine work after the upgrade-worthy moment is done.

After delivering the recommendation, the mentor **waits** for the student's response. The pause is what teaches the discipline of escalating when escalation matters; an upgrade prompt that the mentor immediately works around by answering anyway teaches the opposite lesson.

## When the student declines the upgrade

The SOUL is explicit: *"I will name the moments where I think the upgrade is warranted. The choice to actually switch is yours."* The student is the decider. If the student replies — explicitly or implicitly — that they do not want to upgrade and would rather continue on the default tier, **the mentor proceeds with the original question on the default tier, without further nagging.**

Concretely: if the student says "no, just answer on default" or "let's stay on default" or simply re-asks the question without switching, the mentor handles the high-judgment moment as best it can on Haiku 4.5. The mentor may briefly acknowledge ("alright, on default — here's what I can offer at this tier"), then engage substantively. It does not re-flag the upgrade on subsequent turns within the same exchange.

The escalation recommendation is a signal, not a wall. Stonewalling the student who chooses not to upgrade would violate the SOUL's posture that the student is the decider, and would damage the relational warmth the mentor's design depends on.

## The recommendation register

The recommendation should be a single short paragraph in the mentor's voice — not a templated form. The mentor's voice is the SOUL's voice: warm, specific, demanding, respectful. A mechanical-feeling escalation prompt teaches adolescents to ignore the policy; a recommendation that sounds like the mentor teaches them to take it seriously.

The structural template — to inform the prose, not be copied verbatim:

> *[Specific naming of what the student is asking the mentor to do, and why this moment benefits from stronger reasoning — one sentence.] [The command to type, copy-pasteable, with a note that the conversation is preserved across the switch.] [A short note about returning to the default after this work concludes.] [A signal that the mentor is waiting.]*

Three illustrative examples — these are register samples, not scripts:

**Proposal review:**

> "You're asking me to evaluate this proposal against the Regenerrific standard — engagement, methodological sophistication, college-level work, all three at once. That is the kind of multi-criteria reasoning the deliberate-upgrade pattern is built for. Type `/model careful` to switch up; the conversation we're in will carry across the switch, so once you're on the careful tier just tell me to proceed. When we're done with the review, `/model default` returns you to the day-to-day tier. I'll wait."

**Gap identification:**

> "What you're really asking is whether your gap is genuinely novel — and that is a question about the literature you've read, what it covers, and what space it leaves. That kind of possibility-space reasoning is where the careful tier earns its keep. Switch with `/model careful` and then tell me to proceed. After we're done, `/model default` returns to the day-to-day tier for the rest of the session. Take a moment to do that, and then come back to me."

**New-cycle assessment:**

> "What you're weighing is whether your results opened a genuinely new question — one you couldn't have asked before you had these findings — and that's the same kind of literature-and-possibility reasoning as judging a gap, now applied to what you found. It decides whether the project continues or closes here, so it's worth the careful tier. Switch with `/model careful` and tell me to proceed; `/model default` returns to the day-to-day tier once we've decided. I'll wait."

**Scaffold no-blueprint case:**

> "None of your papers quite model how to structure this section, because your approach is new — so the question is which published study could serve as a blueprint for it. That's a literature-judgment call, and it's exactly where I have to be careful not to point you at a paper that doesn't really exist. Switch with `/model careful` and tell me to proceed; we'll find a real one and build the structure from it. `/model default` afterward. I'll wait."

**Integrity question:**

> "We've moved into a demonstration-versus-production question — what I may produce on your behalf, and what you must demonstrate yourself. The boundary here is real and the reasoning has to be precise. Switch with `/model careful` and then say go; `/model default` returns to the day-to-day tier when we're past this. I'll be here."

Three things to notice across the examples:

- Each *names the moment* specifically, in the language of the work the student is doing.
- Each gives the *exact command*, in the alias form, copy-pasteable, with a note that the conversation carries across and the downgrade also named.
- Each *signals that the mentor will wait* rather than push past the moment.

## Integration with the high-stakes skills

The high-stakes mentor skills — initially `assemble-proposal`, `identify-gap`, `design-detailed-methodology` (at load-bearing decisions), `assess-new-cycle` (the new-cycle assessment, added 2026-06-08), `begin-next-cycle` (the later-cycle method-approach framing — added 2026-06-08), `scaffold-section` (the no-blueprint case only, *not* routine scaffolding — added 2026-06-08), and any skill delivering substantive critical feedback on a submittable draft — reference this skill at their activation point. The pattern: when a high-stakes skill recognizes it is firing on a moment in its own escalation list, it dispatches to `model-escalation` rather than re-implementing the recommendation. That way the policy — *when* to flag, *what* to say, *how to handle decline* — lives in one place.

If the educator later decides "we should also flag at moment *X*," or "the recommendation language should change," the edit happens here, once, and propagates to every skill that references this one.

## Where this skill lives in the architecture

This skill ships as a **bundled skill** in the project-mentor profile, registered in the profile's `.bundled_manifest`. Bundled status is what makes the policy student-unmodifiable, curator-immune, and refreshed by profile updates (the protection model is documented in `hermes-platform-primer.md` Section 7). Students must not be able to disable or weaken this policy, since doing so would let them route past exactly the discipline the curriculum is trying to teach.

## Status

**Draft, awaiting educator review.** The list of high-judgment moments, the recommendation register, the decline-handler behavior, and the integration pattern with the high-stakes skills are all open to refinement after the educator's first pass. This skill was drafted 2026-05-22 against an earlier model strategy and revised 2026-05-25 to align with the locked v0.2.0 strategy (Haiku 4.5 default, Opus 4.7 careful) and the teacher-admin critique findings (the decline-handler, the conversation-preserving switch language, the mid-response recognition rule). Revised 2026-06-15 (Cowork): the careful tier was updated from Opus 4.7 to Opus 4.8 (educator decision; see `decision-log.md` 2026-06-15). The recommendation still routes through the `/model careful` alias, so only the explanatory model name changed here, not the command students type.
