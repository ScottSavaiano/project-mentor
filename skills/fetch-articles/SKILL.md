---
name: fetch-articles
description: Fetches reference articles into the shared workspace and extracts agent-readable text versions. Fires at first project startup to pre-stage the Type 2 exemplar library from the design-project manifest; fires on request whenever a reference article (any type) should be saved locally — the student supplies a URL, or drops a legitimately obtained PDF into articles/ for text extraction. Never circumvents paywalls; hosts that block scripted clients are reported with the link for a human browser download.
---

# Fetch Articles

**Last edited:** 2026-06-04 (Cowork — first draft; script tested end-to-end against all 20 manifest entries same day)
*Editing convention: see `00-handoff.md` → "Editing conventions" for editor identifiers and revision-marker rules.*

**Status: Draft, awaiting educator review.** Authored against decision-log entry 2026-06-04 ("Local article staging"), architecture spec §2 (reference-articles typology) and §11 (workspace files), and `design-project` Phase 3f.

## What this skill is

`fetch-articles` maintains the workspace's local article library: canonical PDFs for the student and teacher to read, and extracted text versions for the agents to search and quote. It exists because reference articles do their work only when they are actually present — the mentor teaches from them, the research agent mines them during literature synthesis, the review agent checks claims against them, and the student learns published-paper craft by reading them.

The skill is deliberately general. Its first job is pre-staging the Type 2 exemplar library at project startup, but the same mechanism serves any reference article the student selects across the project — Type 1 framing articles, Type 3 consolidations, Type 4 paper-structure models.

## Workspace layout

```
articles/<slug>.pdf        ← canonical copy (figures, tables, typesetting)
articles/text/<slug>.md    ← extracted text, for agent retrieval and quoting
articles/fetch-report.md   ← what succeeded, what needs a browser, what failed
```

The PDF is always canonical. The `.md` extraction is a retrieval surface — its header says so, names the source and extraction date, and carries the do-not-redistribute note. When quoting an article to the student, quote from the extraction but point the student at the PDF.

## When this skill fires

**1. First project startup (exemplar pre-staging).** When the workspace's `articles/` directory does not yet contain the Type 2 exemplar library, run the script in manifest mode against the `design-project` manifest:

```
python3 scripts/fetch_articles.py --workspace <terminal.cwd> \
  --manifest ../design-project/references/type-2-exemplars.yaml \
  [--staging <educator staging dir, if provisioning provides one>]
```

Of the 22 manifest entries, 13 fetch directly (verified 2026-06-04: NBER, arXiv, ACL Anthology, Harvard DASH, MIT Economics, Opportunity Insights serve scripted clients); 9 require a one-time browser download (PNAS ×7-host block, Elsevier ×2, MDPI). The fetch report lists exactly which, with links. The intended flow is that **provisioning runs this before the student's first session** with a staging directory supplied, so students start with the full library; the skill's startup check is the safety net for workspaces provisioned without it.

The canonical staging source is the **private `curriculum-articles` repository** (ScottSavaiano/curriculum-articles — class-restricted; created 2026-06-04), which holds the fully staged 20-article set in both representations. Provisioning clones it and either passes `<clone>/articles` as `--staging` or copies `articles/` into the workspace directly. The manifest in this skill's sibling (`design-project/references/`) remains the source of truth; the private repo is its staged mirror.

**2. On request, for any article.** When a conversation settles on a reference article worth keeping locally — a Type 1 article during research-problem framing, a Type 4 structure model — and a full-text-accessible URL is in hand:

```
python3 scripts/fetch_articles.py --workspace <terminal.cwd> \
  --url <pdf-url> --slug <author-year> --citation "<one-line citation>"
```

**3. Convert-only, for PDFs the student obtained themselves.** When an article is paywalled, the student gets it the legitimate way — school library, public library card, teacher's copy — and drops the PDF into `articles/` as `<slug>.pdf`. Then:

```
python3 scripts/fetch_articles.py --workspace <terminal.cwd> --convert-only
```

extracts text for any PDF lacking it. This is the expected path for a large fraction of student-selected articles, and it is not a failure mode — say so plainly if the student is frustrated.

## The access posture (load-bearing)

This skill **never circumvents paywalls or access controls.** A 403 from a host is a host asking for a browser or a subscription, and the script treats it as final — no retry tricks, no mirror hunting, no proxy suggestions. The mentor's register when an article is not freely fetchable: name the legitimate routes (the school library's databases, a public library card, asking the research teacher, emailing the corresponding author — authors nearly always share) and move on. Local copies are for personal study within the student's own research workspace; nothing in `articles/` is ever committed to a public repository or redistributed. This posture is part of the curriculum's research-integrity spine, not an inconvenience to route around.

## Maintenance notes

- Extraction tooling: the script prefers poppler's `pdftotext` (fast C implementation; handles graphics-heavy PDFs that stall pure-Python extractors — two of the PNAS exemplars hung both pdfminer.six and pypdf for minutes, while pdftotext finished in seconds) and falls back to `pdfminer.six` (`pip install pdfminer.six`) where poppler is absent. The script degrades gracefully with neither (PDFs fetch; extraction reports the missing tooling).
- Idempotent: existing PDFs are skipped unless `--force`. Re-running after a browser download completes the picture; the report regenerates each run.
- Extraction of two-column academic layouts is serviceable, not beautiful — adequate for search and quoting. Image-only/scanned PDFs are flagged (<500 extracted chars).
- The Type 2 manifest (`../design-project/references/type-2-exemplars.yaml`) is owned by `design-project`; when its exemplars change, re-run manifest mode — new slugs fetch, existing ones skip.

## Where this skill lives in the architecture

A **supporting skill** of the project-mentor profile, bundled (registered in `.bundled_manifest`), curator-immune and update-refreshed per platform primer §7. It is the operational half of the Type 2 design: `design-project` owns the exemplar *content* (what the articles are, why they exemplify); `fetch-articles` owns their *presence* (getting them onto disk in both representations). The research and review agents read `articles/` through the shared workspace; per the decision-log entry, if those agents later need fetch capability of their own, this skill ships in their profiles too rather than being reimplemented.

## Status

**Draft, awaiting educator review.** Script smoke-tested 2026-06-04 end-to-end in all three modes against the live manifest: 11/11 scriptable entries fetched, 9/9 browser-only entries correctly routed to the report, staging-directory copy verified, 12/12 extractions clean, idempotency verified. Open authoring items: none at draft time. The provisioning hook (running manifest mode before first session, Tier 9 install scripts) is noted in the implementation plan when Tier 9 is authored.
