---
name: hiring-methodology
description: Core methodology for the hiring-assistant plugin - bias-aware evaluation principles, scoring thresholds, agent routing, folder conventions and output formats for evaluating software engineer and engineering manager candidates. Use this skill whenever the user is evaluating resumes, scoring candidates, preparing or assessing interviews, comparing candidates, writing HR summaries, or asks how the hiring framework works, even if they do not name the framework.
---

# Hiring Methodology

This skill replaces the repo-level `CLAUDE.md` / `AI.md` from the original project, which do not load automatically from a plugin. Read `references/methodology.md` for the full source of truth (principles, scoring, file formats, quality checklist). Read `references/config-schema.md` only when editing or validating `config.yaml`. The files in `examples/` are sample outputs for format reference; never treat them as config or candidate data.

## Where data lives

Everything organization- or candidate-specific lives in the user's current project, never in the plugin:

- `.org/<org-name>/` - config.yaml, rubrics/, matrices/, regional/ (ignore `.org/example/` whenever another org folder exists)
- `resumes/developers/new/<Level>/` and `resumes/managers/new/<Level>/` - intake; the subfolder name must match a configured role level
- `evaluations/developers|managers/YYYY-MM-DD_<status>_<name>_<score>-<max>/` - outputs
- `reports/` - batch summaries and CSV

If no `.org/` folder with a real org exists, stop and point the user to `/hiring-assistant:hiring-setup` before evaluating anything.

## Routing

- Titles containing Manager, Director, VP, Head of -> `manager-evaluator` agent
- Engineer, Developer, Architect -> `ic-evaluator` agent
- Candidate from a configured region -> evaluators pull in `regional-education-advisor` and `regional-resume-analyst`
- Presentation feels "off" -> `cultural-bias-advisor`
- Before an interview -> `interview-prep`; after -> `interview-assessor`
- Choosing between finalists -> `candidate-comparer`
- After any evaluation -> `candidate-summary-distiller` (phase=resume or phase=interview)
- After a batch -> `fact-checker`, then `batch-reporter`

Full batch workflows: `/hiring-assistant:evaluate-resumes` and `/hiring-assistant:assess-interviews`.

## Non-negotiables

- Evaluate substance: measurable accomplishments, depth with required tech, code-quality practices, problem-solving evidence, growth trajectory.
- Ignore photos, age, marital status, religion, personal details, resume length, formatting and tone.
- Contextualize cultural presentation; never lower the bar.
- Every factual claim (dates, gaps, durations) must be traceable to the source document; run the fact-checker before reports go out.
- The output is decision support for a human reviewer, not a hiring decision.

## Context management for batches

- Run `/compact` before processing more than 10 candidates and between large batches.
- Run at most 5 agents in parallel; wait for a batch to finish before starting the next; do not poll background agents repeatedly.
- Agents return one-line completion messages ("Created evaluation for X. Score 28/35. Recommendation: Interview."), not the full evaluation.
