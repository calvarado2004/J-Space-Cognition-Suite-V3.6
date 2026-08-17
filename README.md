# J-Space Cognition Suite V3.6

> A model-agnostic inference-time control suite for deep reasoning, long-horizon work, tool use, verification, and recovery.

> **Fork note.** This is an English-only edition of the upstream repository
> [Tiger3807861189/J-Space-Cognition-Suite-V3.6](https://github.com/Tiger3807861189/J-Space-Cognition-Suite-V3.6).
> The upstream README is bilingual: every Chinese paragraph restates the English paragraph next to it.
> This edition translates and merges that content into a single English text. No technical claim,
> number, or protocol rule is changed. The Chinese characters that remain in
> `j-space/modules/deep-reasoning.md` and `j-space/references/j-space-science.md` are worked
> examples of cross-language representation (小 / 大), not prose, so they are kept as they are.

J-Space Cognition Suite turns a language model's accessible working representations into a deliberately managed workspace. It provides a compact operating protocol for selecting what stays active, preserving constraints across long tasks, externalizing durable state, detecting reasoning failure, and returning verified results in clean language.

The suite changes no model weights, requires no fine-tuning, and adds no hidden service. It is an **inference-time cognitive control layer**: text establishes the operating frame, modules route computation, and an optional local controller preserves state between task seams.

J-Space is not merely a Skill; it is an inference-time cognitive control system packaged as a Skill to support cross-platform use, selective loading, and low-friction integration.

---

## Contents

- [Why J-Space](#why-j-space)
- [Core mechanisms](#core-mechanisms)
- [Quick start](#quick-start)
- [Installation](#installation)
- [Operating modes](#operating-modes)
- [Optional controller](#optional-controller)
- [Benchmarks](#benchmarks)
- [Cross-model reproducibility](#cross-model-reproducibility)
- [Project structure](#project-structure)
- [Scientific scope](#scientific-scope)
- [Design principle](#design-principle)
- [Update notes and version lineage](#update-notes-and-version-lineage)
- [Citation](#citation)
- [License](#license)

---

## Why J-Space

Interpretability research describes a privileged internal representational space associated with concepts a model is **poised to say**: contents that can be reported, deliberately held, used in intermediate reasoning, and broadcast into multiple downstream computations. The suite uses **J-space** as the operational name for that accessible workspace.

The engineering problem is not simply whether a model contains the relevant knowledge. It is whether the right representation is loaded strongly enough, remains available when the task becomes mechanical, reaches every dependent sub-task, and is checked before commitment.

J-Space addresses four recurrent sources of inference-time loss:

1. **Working-set overload** — too many live constraints compete for limited active capacity.
2. **Representation drift** — a goal, definition, or invariant changes across steps or files.
3. **Uncontrolled retry** — a failed route is repeated without carrying a diagnosis.
4. **Premature completion** — fluent output is mistaken for verified completion.

---

## Core mechanisms

### 1. Selective workspace loading

The active stage is limited to one or two coherent items. Each item is loaded by stating it, stating the fact that makes it relevant, and using it immediately. Everything else remains automatic or is externalized into the ledger.

### 2. Broadcast hub

Shared names, values, constraints, and style anchors are derived once and read by every dependent branch. This reduces independently reconstructed copies and cross-file inconsistency.

### 3. Dense Track

Long reasoning chains may use a compact private register built from stable symbols, short constraints, and explicit epistemic states:

- `✓` — verified, and able to name the evidence that verified it
- `?` — asserted, but not yet usable as a downstream premise
- `✗` — refuted, with the killing evidence retained
- `??`, `?!` — further annotations on the unchecked state

The Dense Track is not decorative shorthand. Every line must be losslessly expandable into plain language. It remains internal; user-facing output returns to a complete outer register.

### 4. Bridge-before-conclusion reasoning

Intermediate concepts are required to become active before the conclusion that consumes them. This reduces conclusion-first rationalization and makes long chains inspectable at their load-bearing steps.

### 5. Metacognitive control

Confidence, inconsistency, missing constraints, and degeneration signals must select an action: trust, retry with diagnosis, take an independent route and reconcile, or move to empirical verification. A monitoring signal that changes no action is treated as commentary, not control.

### 6. Empirical escape and verification

When derivation stops producing constraints, the suite converts the unknown into a finite candidate set, builds an independent reference, performs differential tests, and records both the verifier and its coverage. This prevents unproductive reasoning from consuming the entire budget.

### 7. First-person agency and functional echo

`I` is used for perception, judgement, and commitment. `we`, `let's`, and `we need` are used only when the model and its workspace coordinate an operation. These statements recur later as protocol actions, checks, and settles. The repetition is a functional **echo**, not a slogan.

This is control grammar, not a claim about consciousness. Its purpose is to bind an accessible state description to a concrete next action.

---

## Quick start

### Skill-aware environments

1. Install the `j-space/` directory in the environment's skill directory.
2. Ask the agent to use `j-space`. If the host exposes Skills as slash commands, invoke `/j-space`.
3. Give the task normally. The gate selects `fast`, `full`, or `loop`.

```text
/j-space
Audit this repository, preserve the current architecture, verify every finding,
and carry the work across all affected files.
```

### Generic model integration

For an agent environment without a native Skill loader, provide `j-space/SKILL.md` as a system-level or developer-level instruction and expose `modules/` and `references/` through its file or retrieval tools. A plain chat or API model cannot resolve local paths by itself: the caller must retrieve and inject the selected files. Do not concatenate every module into every request; selective loading is part of the design.

---

## Installation

The runtime has no third-party dependency. Copy the complete `j-space/` directory so that relative module and reference paths remain intact.

### Windows PowerShell

```powershell
$skillsDirectory = "C:\path\to\skills"
New-Item -ItemType Directory -Force -Path $skillsDirectory | Out-Null
$target = Join-Path $skillsDirectory "j-space"
if (Test-Path -LiteralPath $target) { throw "Target already exists: $target" }
Copy-Item -Recurse -LiteralPath .\j-space -Destination $target
python "$target\scripts\verify_suite.py"
```

### macOS / Linux

```bash
skills_directory="/path/to/skills"
mkdir -p "$skills_directory"
target="$skills_directory/j-space"
if [ -e "$target" ]; then echo "Target already exists: $target" >&2; exit 1; fi
cp -R ./j-space "$target"
python3 "$target/scripts/verify_suite.py"
```

After installation, the suite exposes one canonical entry named `j-space`; a host may present it as `/j-space` or select it through its own Skill interface. The Python controller is optional and uses only the standard library.

---

## Operating modes

| Pass | Selection rule | Loaded machinery |
|---|---|---|
| `fast` | One step, or checkable in one glance | None |
| `full` | Several dependent steps, one bounded deliverable | One or two relevant modules; `ship` if used |
| `loop` | Multiple stages, files, turns, tools, or persistent state | Ledger, seam refresh, checkpointing, register audit, recovery |

A request for brevity may shorten the answer, but it does not lower verification below the gate's floor. This keeps the suite light on simple work without weakening difficult work.

---

## Optional controller

`j-space/scripts/jspace.py` externalizes loop state into `.jspace/` in the current task workspace. Invoke it by its resolved Skill path while keeping the task workspace as the current directory.

```bash
python <skill-root>/scripts/jspace.py note --goal "what done means" --next "first action"
python <skill-root>/scripts/jspace.py seam
python <skill-root>/scripts/jspace.py note --check "what now holds" --by "verifier and coverage"
python <skill-root>/scripts/jspace.py ship OUTPUT_FILE
python <skill-root>/scripts/jspace.py resume
```

The controller provides:

- atomic UTF-8 ledger and history writes
- stable checkpoint and open-question identifiers
- explicit Core live-slot swaps
- verifier-and-coverage requirements for checkpoints
- full state reload after long gaps
- UTF-8 and BOM-aware outgoing-register inspection

It records and reports state; it does not choose the solution. Short tasks should not run it.

Maintainers can verify the complete controller lifecycle from the package root with `python -m unittest discover -s tests -v`. The tests use temporary workspaces and the Python standard library only.

---

## Benchmarks

> 📊 **Full evaluation report** — See [DeepSeek V4 × J-Space Capability Realization Report](https://github.com/Tiger3807861189/DeepSeek-V4-J-Space-Capability-Realization-Report) for the complete benchmark evidence, capability-realization analysis, and chain-of-thought diode discussion.

All values use the native score of the corresponding benchmark; higher is better. `—` means that no result is reported. HLE is separated into no-tool and tool-enabled conditions.

### Evaluation protocol

- Comparator values are vendor-reported results from the corresponding model providers and retain each provider's published evaluation method. They provide a capability reference, not a claim that every column was produced by one shared harness.
- Source records are the [DeepSeek V4-Flash-0731 model card](https://huggingface.co/deepseek-ai/DeepSeek-V4-Flash-0731), Z.ai's GLM-5.3 release evaluation, the [Kimi-K3 model card](https://huggingface.co/moonshotai/Kimi-K3), and Anthropic's [model system cards](https://www.anthropic.com/system-cards).
- Both DeepSeek columns use the official DeepSeek Harness in minimal mode with `max` reasoning effort, `temperature = 1.0`, and `top_p = 0.95`. The J-Space column keeps that evaluation procedure unchanged and loads J-Space as the inference-time control layer.
- The J-Space values are single-run results. No multi-run averaging or confidence interval is implied.
- The base model, benchmark implementation, tool condition, and scoring rule are preserved; J-Space changes the inference-time operating protocol rather than the model weights.

### Model comparison

| Benchmark | DeepSeek V4-Flash-0731 | **DeepSeek V4-Flash-0731 + J-Space V3.6** | GLM-5.3 | Kimi-K3 | Opus-4.8 | Fable 5 (w/ fallback) |
|---|---:|---:|---:|---:|---:|---:|
| HLE (w/o tools) | 37.8 | 45.5 | — | 43.5 | 49.8 | **53.3** |
| HLE (w/ tools) | 51.5 | 60.6 | 62.5 | 56.0 | 57.9 | **63.0** |
| Terminal Bench 2.1 | 82.7 | 87.1 | 88.2 | **88.3** | 85.0 | 88.0 |
| NL2Repo | 54.2 | **70.2** | 58.0 | 58.0 | 69.7 | — |
| CyberGym | 76.7 | 81.7 | **84.5** | 80.0 | 78.3 | 83.1 |
| DeepSWE | 54.4 | 67.4 | 66.9 | 67.5 | 58.0 | **70.0** |
| Toolathlon-Verified | 70.3 | 77.7 | 73.0 | 76.5 | 76.2 | **77.9** |
| Agents' Last Exam | 25.2 | **30.1** | 28.5 | 27.6 | 25.7 | 23.8 |
| AutomationBench (Public) | 25.1 | 31.7 | **48.2** | 30.8 | 27.2 | 29.1 |

Bold marks the highest reported score in each row.

The performance of DeepSeek V4-Flash-0731 + J-Space V3.6 is on par with GLM-5.3 and Kimi-K3, and surpasses Opus-4.8.

### Efficiency results

These task-level ratio indices retain the same task and model conditions; higher is better. Speed is `benchmark score / elapsed time`; token efficiency is `benchmark score / consumed tokens`. Elapsed time and token count use fixed, uniform scaling coefficients across Control and J-Space; the coefficients affect the displayed scale but not the relative Gain, so they are intentionally omitted. The Gain column is `J-Space / Control`.

| Metric | Control | J-Space | Gain | Interpretation |
|---|---:|---:|---:|---|
| Speed | 0.43 | **1.09** | **2.53×** | Re-encoding, stall routing, and checkpoint recovery reduce backtracking and restart cost. |
| Token Efficiency | 0.38 | **0.84** | **2.21×** | Dense Track compression, state externalization, and diagnosis-carrying retries reduce redundant derivation. |

### What the benchmarks exercise

| Benchmark | Primary capability measured | J-Space mechanism |
|---|---|---|
| HLE | Knowledge-intensive, multi-step reasoning; the tool condition adds retrieval and verification. | Bridge-before-conclusion, metacognitive control, Empirics, and coverage-aware verification. |
| Terminal Bench 2.1 | Long-horizon terminal operation under partial feedback. | Loop state, `Next`, checkpoints, marker-bound recovery, and register audits at tool seams. |
| NL2Repo | Translating natural-language requirements into coherent repository-wide changes. | Broadcast hub, Core swaps, capacity control, and cross-file constraint preservation. |
| CyberGym | Cybersecurity diagnosis, tool interaction, and evidence-driven correction. | Named unknowns, differential testing, contradiction markers, and dead-end escape. |
| DeepSWE | Repository-scale software engineering and iterative verification. | Dense Track, ledger continuity, diagnosis-carrying retries, and done-check. |
| Toolathlon-Verified | Multi-tool orchestration with verifiable outcomes. | Seam refresh, shared state, checkpoint coverage, and clean outer-register delivery. |
| Agents' Last Exam | Composite agentic reasoning under heterogeneous task demands. | Adaptive pass selection, selective module loading, monitoring, and recovery. |
| AutomationBench | Persistent workflow execution and dependency management. | Goal anchoring, stable Open identifiers, explicit `Next`, hand-offs, and long-gap resume. |

The results show a task-dependent pattern. Gains are strongest where failures arise from state drift, capacity pressure, repeated tool use, or incomplete verification. Knowledge unavailable to the underlying model is not created by the suite.

---

## Cross-model reproducibility

The operating effects have been reproduced across the **DeepSeek, Qwen, GLM, GPT, and Claude** model families. The suite does not depend on a vendor-specific API, tokenizer, hidden-state probe, or training recipe. Its portable unit is the protocol: workspace loading, selective routing, state externalization, verification, and recovery.

Cross-model reproducibility does not imply identical gains. Base capability, context policy, tool harness, sampling configuration, and benchmark implementation still affect the final score.

---

## Project structure

```text
J-Space-Cognition-Suite-V3.6/
├── .gitignore                      # excludes local ledger and Python cache state
├── LICENSE                         # Apache License 2.0
├── README.md                       # English user and evaluation guide
├── tests/
│   └── test_jspace.py              # standard-library controller regression tests
└── j-space/
    ├── SKILL.md                    # single entry, gate, routing, invariants
    ├── modules/                    # nine selectively loaded protocols
    ├── references/                 # evidence, induction, and exemplars
    └── scripts/
        ├── jspace.py               # optional loop controller
        ├── workspace-ledger.md     # ledger template and contract
        └── verify_suite.py         # authoring-time integrity check
```

`SKILL.md` is the only registered entry. Modules and references are loaded on demand, preserving context efficiency and preventing the control system from becoming its own source of overload.

---

## Scientific scope

The fundamental scientific foundation of J-Space is grounded in Anthropic's related research. J-Space is an engineering suite grounded in mechanistic observations and behavioral evaluation. It does not claim that text instructions directly expose every hidden activation, and it does not equate first-person language with consciousness. It uses observable functional properties — reportability, deliberate maintenance, intermediate computation, broadcast, monitoring, and causal sensitivity — as the basis of an inference-time control interface.

The complete research interpretation, evidence boundaries, and cited mechanisms are documented in [`j-space/references/j-space-science.md`](j-space/references/j-space-science.md).

---

## Design principle

> **Dense on the inside, decodable on demand, clean on the outside.**

Use only the machinery the task earns. Let automatic work remain automatic; bring structure onto the stage when complexity, duration, or verification risk makes that structure pay for itself.

---

## Update notes and version lineage

J-Space has progressed through:

**V1 → V1.5 → V1.8 → V2 → V2.5 → V2.6 → V3 → V3.1 → V3.2 → V3.5 → V3.5Turbo → V3.6**

This lineage represents repeated engineering validation rather than a sequence of cosmetic labels. The suite has undergone multi-round revision, controlled comparison, ablation analysis, cross-model reproduction, protocol consistency review, and executable controller testing.

The current suite is a mature, bounded system: one entry, nine focused modules, three supporting references, one optional runtime controller, one authoring-time verifier, and one standard-library
regression suite. Its maturity comes from repeated falsifiable testing and scope discipline, not from adding more procedure.

---

## Citation

[![DOI](https://zenodo.org/badge/1308234922.svg)](https://zenodo.org/badge/latestdoi/1308234922)

If you use J-Space in your research, please cite the accompanying paper (link to be added upon publication). For engineering use, cite the upstream repository as:

> Tiger3807861189. (2026). *J-Space Cognition Suite V3.6* (Version 3.6). Zenodo. https://doi.org/10.5281/zenodo.21971181

```bibtex
@software{j-space-cognition-suite,
  author       = {Tiger3807861189},
  title        = {{J-Space} Cognition Suite V3.6},
  year         = {2026},
  version      = {3.6},
  doi          = {10.5281/zenodo.21971181},
  url          = {https://github.com/Tiger3807861189/J-Space-Cognition-Suite-V3.6},
  note         = {Licensed under Apache License 2.0}
}
```

The academic analysis will be presented in the accompanying paper. For engineering use, the upstream repository and its DOI above are the canonical reference.

Companion evaluation report: [DeepSeek V4 × J-Space Capability Realization Report](https://github.com/Tiger3807861189/DeepSeek-V4-J-Space-Capability-Realization-Report).

---

## License

J-Space Cognition Suite is released under the [Apache License 2.0](https://www.apache.org/licenses/LICENSE-2.0). It permits use, modification, redistribution, and commercial integration under its notice and patent terms. See [`LICENSE`](LICENSE) for the complete terms.
