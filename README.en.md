[**English**](README.en.md) | [简体中文](README.md)

<div align="center">

# Arch Audit Pipeline

**Truly understand a codebase: ⓪ snapshot → ①-⑥ understand & audit → ⑦ diagrams → ⑧ three-tier docs. Turns this job from "depends on the agent's mood" into "depends on the program" — every step ships artifacts, artifacts meet sand-grain acceptance, numbers come from scripts, claims carry file:line, and below 100% completion you don't get to call it done.**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](#license)
[![Pipeline](https://img.shields.io/badge/pipeline-⓪→⑧_nine_steps-blue)](#a-project-end-to-end)
[![Discipline](https://img.shields.io/badge/discipline-ten_locks-red)](#core-stance)
[![Acceptance](https://img.shields.io/badge/acceptance-sand_grain-critical)](#core-stance)
[![Scoping](https://img.shields.io/badge/scoping-deterministic_ladder_S1→S5-blueviolet)](#a-project-end-to-end)
[![Probe](https://img.shields.io/badge/scope_probe-byte_identical_reruns-success)](#a-project-end-to-end)
[![Coverage](https://img.shields.io/badge/coverage_dual_check-reverse_0_leak-success)](#a-project-end-to-end)
[![Diagrams](https://img.shields.io/badge/diagrams_global_·_per_module_20–35-blue)](#a-project-end-to-end)
[![Perf](https://img.shields.io/badge/profiling-cProfile_same_source_flame-orange)](#a-project-end-to-end)
[![Docs](https://img.shields.io/badge/docs-lossless_L2·L1·L0-green)](#-three_tier_docs)
[![References](https://img.shields.io/badge/fabricated_refs-forbidden-9cf)](#core-stance)
[![Completion](https://img.shields.io/badge/completion-below_100%25_no_delivery-orange)](#resume-protocol)
[![Resume](https://img.shields.io/badge/flow-resumable_state_machine-informational)](#resume-protocol)
[![Degradation](https://img.shields.io/badge/missing_deps-full_pipeline_anyway-success)](#dependencies)

[What problem it solves](#what-problem-it-solves) · [What makes it different](#what-makes-it-different) · [A project end-to-end](#a-project-end-to-end) · [Core stance](#core-stance) · [Resume protocol](#resume-protocol) · [Quick start](#quick-start) · [FAQ](#faq) · [Repo layout](#repo-layout) · [License](#license)

</div>

## What problem it solves

Throw "help me understand this project" at an agent and you hit three walls:

1. **It evaporates.** The agent holds forth on "clean layering" with zero artifacts on disk — in three months you (or the next agent) start from scratch.
2. **It slacks halfway.** Past the midpoint, steps start vanishing: diagrams "later", docs "for brevity", audit "conclusions are clear" — each excuse reasonable, the sum a half-finished mess.
3. **Confident, but unverifiable.** "Zero coupling between the cores." "No circular imports." Ask for file:line and it starts hedging.

This skill's answer in one sentence:

> **Understanding the architecture is the primary goal; auditing is the quality gate that backs it. ⓪→⑧ runs in full by default: every step ships artifacts, every number comes from a script, every claim carries file:line, and only 100% completion closes the job.**

## What makes it different

- **Sand-grain acceptance.** Artifacts are accepted at the smallest independently verifiable unit — one dependency edge, one file:line, one file, one claim. Handbooks define the granularity floor only: **structure is free to vary, granularity is not**. You never get a filled-in template.
- **Ten execution locks.** Serial gates; the execution log *is* the evidence (no log entry = step never happened); every number script-computed (even zero results must show the check); per-step acceptance floors; pre-delivery self-audit; readable artifacts (no codebooks); a single output root; a resumable state machine; scoping authority belongs to the user alone.
- **Scoping is a program, not a vibe.** Deterministic signal ladder (git index → package manifests → index files → import closure → connected components) + a `scope-probe` script emitting three lists + **byte-identical reruns** + dual coverage checks (reverse coverage = 0 leaks).
- **Completion hard gate + resumable state machine.** A nine-step state table with n/9 heartbeats; after any interruption, resume from the first non-done step and never redo finished work; **below 100% completion you cannot mark delivery**; scope cuts require the user's ruling, quoted verbatim into the log.
- **Lossless three-tier docs (L2/L1/L0).** The same lossless information at three language levels: technical (source precision) / plain-language (textbook grade, terms cite real literature) / paramecium (ultra-short, picture-book). **Metaphor is a translation layer, not a filter** — three information-fidelity checklists, each ticked independently.
- **Diagrams go where the drilling goes.** A global set (container / sequence / ER / dependency — the minimum four) **plus a per-module set for every drilled module** (architecture / dependency / sequence / ER, tiered coverage to prevent diagram explosions, budget 20-35); dependency diagrams are always script-generated in batch; flame graphs come from real profiles — never invented.
- **Profiling is a fixed audit stage.** Run the real core paths, capture with cProfile → `perf-report.md` + flame graph → findings flow into the audit report → synced into all three docs. Charts and numbers share one source of truth; two sets of numbers are forbidden.
- **Degrade without losing quality; upgrade without skipping.** Missing dependencies trigger built-in fallbacks (grep edge tables + hand-rolled Tarjan / layer-matrix checks) while the full ⓪→⑧ still runs; **when a first-choice dependency IS available you must actually run it** and cross-check against the fallback — "installed but never ran" is not a defense.

## A project end-to-end

| Stage | Steps | Artifacts |
|---|---|---|
| Front | ⓪ project discovery & snapshot bundling (signal ladder S1-S5 + scope-probe + dual coverage) | `0-bundle/` read-only snapshot + README-BUNDLE + three probe lists |
| Understand & audit | ① scoping → ② static mapping → ③ layered drilling → ④ inference + **profiling** → ⑤ audit → ⑥ fact-check | `1-scope/` → `6-facts/` (AGENTS / dep-edges / module cards / architecture / perf-report + flame / audit-report / facts-checklist) |
| Deliver | ⑦ diagrams → ⑧ three-tier docs | `7-diagrams/` (global + per-module sets) + `8-documents/` (technical / plain / paramecium) |

**⑤ is an independent quality gate**: with Critical findings unresolved (circular dependencies, collapsed layering, dead extension points), `architecture.md` cannot be marked deliverable. **⑥ is the anti-hallucination backstop**: every claim carries file:line, and falsified claims must be written back before delivery.

## Core stance

- **Auditing is a hard quality gate, not a value-add.** An architecture diagram that only describes "what is" without judging "whether it should be" is an anatomy chart that cannot tell trunk from tumor.
- **The primary goal is understanding.** The measure is whether a reader can take the ⓪→⑧ artifact chain and independently explain: what the system is made of, how parts call each other, why it is designed this way, where the traps are. Every artifact is written to be understood, not to pass.
- **Artifacts are for humans, not codebooks.** Scripts commented section by section, data files documented field by field, every abbreviation expanded at first use — generated output must contain no unexplained invented jargon.
- **Terms need provenance.** The plain-language edition is written to textbook grade: first occurrence of a term carries a plain explanation + English original + a verifiable literature reference; **fabricating references is forbidden** — if unsure, write "industry convention" and explain.

## Resume protocol

The flow is a resumable state machine, not a one-shot tightrope:

1. First action of any session (including a resumed one): read the nine-step state table in `pipeline-log.md`;
2. Continue from the first non-done step; finished steps with artifacts on disk are **never rerun** (except when falsified by ⑥);
3. Update the state table on every landing + heartbeat n/9; big steps subdivide (⑦ m/diagrams, ⑧ a/8 + b/9 + c/7);
4. **Delivery requires all three**: state table all "done" (out-of-scope only by user ruling, quoted into the log), self-audit all green, completion 100%.
5. When context runs low: land current artifacts → update state table → declare an explicit pause point — **never close by skipping**; degradation applies to tools, never to steps.

## Quick start

```bash
# Install (user level)
cp -r arch-audit-pipeline ~/.zcode/skills/
# After restarting the session, tell your agent:
# "Use arch-audit-pipeline to truly understand <project>"
# Partial run? Scope it first ("only ⓪①④⑤ this time") — the ruling is quoted into the log
```

All output converges under one output root `arch-audit-output/`:

```
arch-audit-output/
├── README.md           # artifact navigation & reading order
├── pipeline-log.md     # execution log + nine-step state table
├── 0-bundle/ … 8-documents/   # one directory per step, named step-number + short name
```

## Dependencies

First choices: Superpowers (process skeleton), Cognee (code knowledge graph), Serena (symbol lookup), import-linter (layer rules), the official `docx` skill (three-tier docs). **Wrappers are accelerators, not prerequisites**: on any missing dependency, built-in fallbacks (grep edge tables + hand-rolled Tarjan cycle detection / layer-matrix assertions) carry ⓪→⑧ to completion. See `references/dependency-inventory.md`.

## FAQ

**Why not just ask the agent "what's this project's architecture"?** A one-shot answer has no artifacts, no acceptance, no completion rate — in three months the information is gone. This flow turns "understanding" into a verifiable artifact chain.
**Doesn't auditing slow down understanding?** ⑤⑥ are quality gates for understanding: gates prevent confident errors (false positives, invented line numbers, stale diagrams). Field record: the audit caught a scanner false positive (Tunables) and the audit's own misstatement (falsified by ⑥ and rewritten) — the gates genuinely catch things.
**Windows can't test Linux issues?** All three case-mismatch checks run natively on Windows: static comparison (import spelling vs real filenames) + `PYTHONCASECHECK=1` dynamic rerun + a controlled experiment record; real Linux (WSL/CI) is an optional extra.
**Can it finish in one run?** The flow is a resumable state machine — when context runs low, land artifacts and declare a pause point; next session resumes from the state table. The ⑦ diagram set and ⑧ docs both support incremental completion across rounds.

## Repo layout

| Path | Content |
|---|---|
| [`SKILL.md`](SKILL.md) | Main instructions: ⓪+①-⑥+⑦⑧, ten execution locks, per-step acceptance floors |
| [`references/scope-probing.md`](references/scope-probing.md) | ⓪ deterministic procedure: signal ladder / probe rules / confidence tiers / dual coverage |
| [`references/diagram-playbook.md`](references/diagram-playbook.md) | ⑦ diagram methodology: selection table / four principles / minimum four / per-module sets / archify mapping |
| [`references/doc-playbook.md`](references/doc-playbook.md) | ⑧ three-doc methodology: granularity lists ×3 / textbook grade / DOCX engineering / visual acceptance gate |
| [`references/dependency-inventory.md`](references/dependency-inventory.md) | Dependency inventory with local measured status |
| [`assets/`](assets/) | Interactive flow diagram (archify-rendered; re-render from the source spec) |

## License

[MIT](LICENSE) © 2026
