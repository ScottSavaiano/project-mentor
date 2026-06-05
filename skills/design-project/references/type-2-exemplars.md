# Type 2 Baked-In Exemplars — Method-Approach Reference Articles

**Last edited:** 2026-06-04 (Cowork — first draft; all access links verified full-text-accessible 2026-06-04. Second edit same day: Autor, Dorn & Hanson added to item 4 per educator review. Third edit same day: item 12 "Generative agent-based social simulation" added per the educator-approved menu addition — Park et al. 2023 + OASIS exemplars; the off-menu catch-all renumbered to 13)
*Editing convention: see `00-handoff.md` → "Editing conventions" for editor identifiers and revision-marker rules.*

**Status: Draft, awaiting educator review.** Resolves `design-project` open authoring item 1 (separate-file option, per the volume reasoning recorded there). Authored against architecture spec §2 (Type 2 definition), decision-history §6.1 (the menu), and the Regenerrific Methods Menu document (the mentor's own teaching descriptions).

## How this file is used

This file is the data behind `design-project` Phase 3f. When a method-approach from the Regenerrific menu is being seriously considered, the mentor surfaces that approach's 1–2 exemplar articles below as part of teaching the menu item — per architecture spec §2, "surfaced as part of the mentor's teaching during the menu walkthrough." When the student selects an approach, its exemplars are inherited into `reference_articles.md` as the cycle's Type 2 articles.

Every article here is **full-text accessible without an institutional login** — each entry carries a primary access link and, where one exists, a stable backup. Accessibility was verified 2026-06-04; the access links should be re-verified when this file is next revised (link rot is the maintenance risk of a baked-in set).

This file has a machine-readable companion, `type-2-exemplars.yaml` (same directory), consumed by the `fetch-articles` skill to pre-stage local copies of all exemplars in the student's workspace (`articles/<slug>.pdf` + `articles/text/<slug>.md`) at provisioning or first project startup. Keep the two files in step: an exemplar change here is a manifest change there.

**A note on weight.** Several exemplars study heavy subjects — childhood abuse (Felitti), racial discrimination (Bertrand & Mullainathan; Voigt), midlife mortality (Case & Deaton). This is unavoidable in a set meant to show social science methods doing real work, and these topics may connect to students' lived experience. The mentor's acknowledgment rule (decision-history §3.3; `design-project` edge cases) applies when surfacing them.

---

## 1. Sophisticated regression methods

**Gimbrone, Bates, Prins & Keyes (2022). "The politics of depression: Diverging trends in internalizing symptoms among US adolescents by political beliefs." *SSM – Mental Health* 2, 100043.**
Regression with interaction terms on fourteen years of Monitoring the Future data (N = 86,138 12th-graders), testing whether trends in depressive symptoms diverge by political beliefs and sex. A strong exemplar because the interaction term *is* the finding — the paper shows what it means for one variable's effect to depend on another, on a question about people the same age as the student.
Access: https://www.sciencedirect.com/science/article/pii/S2666560321000438 (gold OA) · backup: https://pmc.ncbi.nlm.nih.gov/articles/PMC8713953/

**Rohrer, Egloff & Schmukle (2015). "Examining the effects of birth order on personality." *PNAS* 112(46), 14224–14229.**
Large-sample regression (N > 20,000 across three national panels) testing birth-order effects both between and within families. A strong exemplar of design-by-comparison-structure: the within-family analysis is what makes the null finding credible, and the paper is a model of how a careful regression study can overturn a popular claim.
Access: https://www.pnas.org/doi/10.1073/pnas.1506451112 (free full text)

## 2. Causal inference designs

**Card & Krueger (1994). "Minimum Wages and Employment: A Case Study of the Fast-Food Industry in New Jersey and Pennsylvania." *American Economic Review* 84(4), 772–793.**
The canonical difference-in-differences study: New Jersey raised its minimum wage, eastern Pennsylvania didn't, and 410 fast-food restaurants on both sides of the border became a natural experiment. A strong exemplar because the identification logic is fully visible to a first-time reader — you can hold the entire design in your head.
Access: https://www.nber.org/system/files/working_papers/w4509/w4509.pdf (free working-paper version; published as AER 84(4))

**Abadie, Diamond & Hainmueller (2010). "Synthetic Control Methods for Comparative Case Studies: Estimating the Effect of California's Tobacco Control Program." *JASA* 105(490), 493–505.**
The paper that introduced synthetic control: when only one unit is treated (California's Prop 99), build a weighted combination of untreated states that tracks pre-treatment California, then measure the divergence. A strong exemplar of inventing a comparison group when nature doesn't supply one.
Access: https://www.nber.org/system/files/working_papers/w12831/w12831.pdf (free working-paper version) · backup: https://www.law.upenn.edu/live/files/8950-abadie2010pdf

## 3. Social epidemiology

**Felitti, Anda, Nordenberg et al. (1998). "Relationship of Childhood Abuse and Household Dysfunction to Many of the Leading Causes of Death in Adults: The Adverse Childhood Experiences (ACE) Study." *American Journal of Preventive Medicine* 14(4), 245–258.**
The founding ACE study: a graded dose–response relationship between adverse childhood exposures and adult health outcomes in 13,494 HMO patients. A strong exemplar of the life-course logic at the heart of social epidemiology — how something in childhood social environment shapes outcomes decades later — and of how a simple exposure score can carry a whole research program. *Weight note applies.*
Access: https://www.ajpmonline.org/article/S0749-3797(98)00017-8/fulltext (free full text)

**Case & Deaton (2015). "Rising morbidity and mortality in midlife among white non-Hispanic Americans in the 21st century." *PNAS* 112(49), 15078–15083.**
Population-level mortality analysis that surfaced the "deaths of despair" pattern from public vital-statistics data. A strong exemplar of descriptive epidemiology done at full rigor — no causal claim, but a population pattern so carefully established that it reset a research agenda. *Weight note applies.*
Access: https://www.pnas.org/doi/10.1073/pnas.1518393112 (free full text)

## 4. Econometrics

**Chetty & Hendren (2018). "The Impacts of Neighborhoods on Intergenerational Mobility I: Childhood Exposure Effects." *Quarterly Journal of Economics* 133(3), 1107–1162.**
Panel data on seven million moving families, with an identification strategy built from *when* in childhood the move happened: outcomes improve linearly with years of exposure to a better area. A strong exemplar of exploiting the structure of how data was generated — the exposure-time design is the kind of identification thinking the menu item describes.
Access: https://scholar.harvard.edu/files/hendren/files/nbhds_paper.pdf (free author-hosted) · backup: https://opportunityinsights.org/paper/neighborhoodsi/

**Autor, Dorn & Hanson (2013). "The China Syndrome: Local Labor Market Effects of Import Competition in the United States." *American Economic Review* 103(6), 2121–2168.**
The "China shock" paper: a panel of US local labor markets over two decades, with an instrumental-variables strategy that isolates the import-competition effect from everything else moving in those economies. A strong exemplar of two of the menu item's named tools at once — panel data methods and an identification strategy built from how the data was generated — on a question whose stakes a student can see in the news.
Access: https://economics.mit.edu/sites/default/files/publications/the%20china%20syndrome%202013.pdf (free author-hosted) · backup: https://www.nber.org/system/files/working_papers/w18054/w18054.pdf

## 5. Experimental and quasi-experimental designs

**Bertrand & Mullainathan (2004). "Are Emily and Greg More Employable Than Lakisha and Jamal? A Field Experiment on Labor Market Discrimination." *American Economic Review* 94(4), 991–1013.**
Nearly 5,000 résumés, identical except for randomly assigned names, sent to real job ads: white-sounding names received 50% more callbacks. A strong exemplar of the field-experiment logic — randomization makes the comparison clean, and the design measures discrimination directly rather than asking about it. *Weight note applies.*
Access: https://www.nber.org/system/files/working_papers/w9873/w9873.pdf (free working-paper version; published as AER 94(4))

**Paluck, Shepherd & Aronow (2016). "Changing climates of conflict: A social network experiment in 56 schools." *PNAS* 113(3), 566–571.**
An anticonflict intervention randomized across 56 New Jersey middle schools (24,191 students), reducing conflict ~30% — with network measurement identifying which students ("social referents") drive norm change. A strong exemplar of a randomized field experiment in a setting students know from the inside; it doubles as a network-analysis exemplar (see item 8).
Access: https://www.pnas.org/doi/10.1073/pnas.1514483113 (free full text)

## 6. Modern computational methods

**Salganik et al. (2020). "Measuring the predictability of life outcomes with a scientific mass collaboration." *PNAS* 117(15), 8398–8403.**
The Fragile Families Challenge: 160 teams used machine learning on rich longitudinal data to predict six life outcomes — and even the best models barely beat simple benchmarks. A strong exemplar because it is *about* what ML can and cannot do in social science: an honest, large-scale test of predictability itself, and a model of humility about computational claims.
Access: https://www.pnas.org/doi/10.1073/pnas.1915006117 (free full text)

**Kosinski, Stillwell & Graepel (2013). "Private traits and attributes are predictable from digital records of human behavior." *PNAS* 110(15), 5802–5805.**
Facebook Likes alone predict personality, demographics, and sensitive attributes with startling accuracy. A strong exemplar of big-data prediction at scale — and a natural opening for the ethics conversation the mentor will want to have about what computational social science makes possible.
Access: https://www.pnas.org/doi/10.1073/pnas.1218772110 (free full text)

## 7. Natural language processing and computational text analysis

**Garg, Schiebinger, Jurafsky & Zou (2018). "Word embeddings quantify 100 years of gender and ethnic stereotypes." *PNAS* 115(16), E3635–E3644.**
Embeddings trained on a century of text, validated against census data, used to measure how stereotypes shifted over 100 years. A strong exemplar of embedding-based analysis "capturing meaning in ways simple word counts cannot" — the menu item's own phrase, demonstrated at historical scale.
Access: https://www.pnas.org/doi/10.1073/pnas.1720347115 (free full text) · backup: https://arxiv.org/abs/1711.08412

**Voigt et al. (2017). "Language from police body camera footage shows racial disparities in officer respect." *PNAS* 114(25), 6521–6526.**
Computational linguistic analysis of body-camera transcripts from routine traffic stops, measuring respectfulness of officer language at a scale human coding could not reach. A strong exemplar of NLP turning transcripts — exactly the kind of data the menu item names — into a measurable social quantity. *Weight note applies.*
Access: https://www.pnas.org/doi/10.1073/pnas.1702413114 (free full text)

## 8. Network analysis methods

**Christakis & Fowler (2007). "The Spread of Obesity in a Large Social Network over 32 Years." *New England Journal of Medicine* 357, 370–379.**
Thirty-two years of the Framingham Heart Study reconstructed as a 12,067-person social network, testing whether obesity spreads person-to-person through ties. A strong exemplar of relational thinking: the unit of analysis is the tie, not the individual — and the methodological debate it sparked about separating influence from homophily is itself instructive.
Access: https://dash.harvard.edu/bitstream/handle/1/3710802/Christakis_SpreadofObesity.pdf (Harvard DASH, free) · backup: https://www.nejm.org/doi/full/10.1056/NEJMsa066082

**Paluck, Shepherd & Aronow (2016)** — cross-listed from item 5. The social-referent analysis is the network half: mapping each school's social network to find the students whose behavior others attend to, then showing the intervention works through them.

## 9. Geo-aware methods

**Hoffman, Shandas & Pendleton (2020). "The Effects of Historical Housing Policies on Resident Exposure to Intra-Urban Heat: A Study of 108 US Urban Areas." *Climate* 8(1), 12.**
Digitized 1930s redlining maps joined to present-day land-surface temperature across 108 cities: formerly redlined neighborhoods run ~2.6°C hotter nationally. A strong exemplar of spatial analysis where location is the substance — a historical policy layer and a physical measurement layer linked through geography, with a method a student can fully understand and partially reproduce.
Access: https://www.mdpi.com/2225-1154/8/1/12 (gold OA)

**Chetty & Hendren (2018), Part II ("County-Level Estimates")** — cross-reference from item 4's exemplar: the companion paper maps causal place effects county by county, a model of taking geographic variation seriously as a finding rather than a nuisance. Access: https://economics.mit.edu/sites/default/files/inline-files/movers_paper2_1.pdf (free).

## 10. Synthetic AI survey participants

**Argyle, Busby, Fulda, Gubler, Rytting & Wingate (2023). "Out of One, Many: Using Language Models to Simulate Human Samples." *Political Analysis* 31(3), 337–351.**
The paper that defined "algorithmic fidelity": conditioning GPT-3 on real respondents' demographic backstories produces "silicon samples" whose response distributions track human subgroups. The architecture specification's own named example for this menu item (spec §2). A strong exemplar both of the method and of careful validation against real survey data — the part a student is most tempted to skip.
Access: https://www.cambridge.org/core/journals/political-analysis/article/out-of-one-many-using-language-models-to-simulate-human-samples/035D7C8A55B237942FB6DBAD7CAA4E49 (open access) · backup: https://arxiv.org/abs/2209.06899

**Horton (2023). "Large Language Models as Simulated Economic Agents: What Can We Learn from Homo Silicus?"**
Runs classic behavioral-economics experiments on LLM "subjects" and compares the results to the original human findings. A strong exemplar of using synthetic participants for *experiments* (not just surveys), with explicit attention to where the simulation breaks down.
Access: https://arxiv.org/abs/2301.07543 (open access)

## 11. Model fine-tuning

**Jiang, Beeferman, Roy & Roy (2022). "CommunityLM: Probing Partisan Worldviews from Language Models." *COLING 2022*.**
Fine-tunes GPT-2 separately on tweets from committed Republican and Democratic partisans, then probes both models with ANES survey questions — the fine-tuned models' "opinions" track the real groups' survey responses. A strong exemplar of exactly what the menu item describes: training on a corpus you can isolate along a dimension, then studying how the model's behavior changes. Feasible at student scale (GPT-2-sized models, public code).
Access: https://aclanthology.org/2022.coling-1.593/ (ACL Anthology, open access) · backup: https://arxiv.org/abs/2209.07065

## 12. Generative agent-based social simulation

**Park, O'Brien, Cai, Morris, Liang & Bernstein (2023). "Generative Agents: Interactive Simulacra of Human Behavior." *UIST 2023*.**
The paper that launched generative agents: twenty-five LLM agents with memory, reflection, and planning inhabit a small simulated town, and social behavior — party invitations spreading, relationships forming — *emerges* rather than being scripted. A strong exemplar of the paradigm's core claim and its core obligation: the believability evaluation is the part a student should study hardest, because validation is this method's hard problem.
Access: https://arxiv.org/abs/2304.03442 (open access)

**Yang et al. (2024). "OASIS: Open Agent Social Interaction Simulations with One Million Agents." (CAMEL-AI.)**
A social-media simulator — dynamic networks, recommendation systems, posting and commenting agents — scalable from hundreds to a million agents, used to reproduce phenomena like information spread and group polarization. A strong exemplar of the system-level form of the method (and the open-source engine behind tools like MiroFish, which is the instrument a student's stretch version might actually run on). Scale-versus-cost is the design lesson: every agent and round is spend.
Access: https://arxiv.org/abs/2411.11581 (open access)

## 13. Other current approaches at the edge of the field

**No baked-in exemplars — by design.** Per `design-project`'s off-menu edge case and decision-history §6.1, Type 2 articles for an off-menu approach come from a literature search with the student, not from this file; the literature review (cycle-template stage 7) is the natural place they surface. This entry exists so the file's coverage of the menu is explicit: twelve items carry baked-in exemplars; the thirteenth (this catch-all) deliberately does not.

---

## Maintenance

This set will date — the menu's own standard ("current examples of what sophistication looks like; the menu will date") applies doubly to its exemplars, and items 10–11 sit in fast-moving territory. When an exemplar is superseded or a link dies, replace it here and note the change per the editing convention. The selection criteria to preserve on replacement: full-text accessible without institutional login; genuinely exemplary of the method on a social science question; readable as a model by a quantitatively strong high school student; and, where possible, studying something a teenager has reason to care about.
