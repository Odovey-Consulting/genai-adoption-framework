# AGENTS.md

This file provides guidance to AI coding agents when working with content in this repository.

## Repository Overview

The GenAI Adoption Framework (GAF) is a **pure documentation repository** — there is no source code, build system, package manager, or CI/CD pipeline. All content is Markdown files and Mermaid diagram sources (`.mmd`). There is nothing to build, test, or lint.

## Architecture

GAF defines **five stacking capability layers** where each layer depends on the one below it. Skipping layers creates structural risk — this is a core design invariant.

```
Layer 5: Agent Security   — autonomous agent identity, authorization, audit
Layer 4: Workloads        — AI application lifecycle (scoping → production)
Layer 3: Operations       — observability, security ops, cost management
Layer 2: Gateway          — centralized control plane for all model traffic
Layer 1: Governance       — policy foundation (data classification, acceptable use)
```

Three **maturity stages** (Foundation → Operational → Optimized) define which layers and capabilities an organization activates progressively. Five **cross-cutting concerns** (risk management, compliance, responsible AI, organizational readiness, tradeoffs) span all layers and stages.

## Content Organization

```
docs/
├── overview.md
├── principles.md
├── glossary.md
├── layers/
│   ├── 01-governance/          — policy foundation
│   ├── 02-gateway/             — centralized model traffic control
│   ├── 03-operations/          — observability, cost, security ops
│   ├── 04-workloads/           — AI application lifecycle
│   └── 05-agent-security/      — autonomous agent controls
│       └── (each layer contains: README.md, principles.md, capabilities.md,
│            best-practices.md, anti-patterns.md, decision-guide.md)
├── maturity-model/
│   ├── README.md
│   ├── stage-1-foundation.md
│   ├── stage-2-operational.md
│   ├── stage-3-optimized.md
│   └── assessment.md
├── cross-cutting/
│   ├── README.md
│   ├── risk-management.md
│   ├── compliance.md
│   ├── responsible-ai.md
│   ├── organizational-readiness.md
│   └── tradeoffs.md
├── reference-architectures/
│   ├── README.md
│   ├── single-cloud.md
│   ├── multi-cloud.md
│   ├── hybrid.md
│   └── air-gapped.md
├── checklists/
│   ├── README.md
│   ├── governance-readiness.md
│   ├── gateway-deployment.md
│   ├── operations-launch.md
│   ├── workload-release.md
│   └── agent-security-review.md
└── lenses/
    └── README.md               — planned, not yet authored
assets/
└── diagrams/
    ├── gaf-framework.mmd
    ├── layer-dependencies.mmd
    ├── maturity-model.mmd
    └── reference-architectures/
        ├── single-cloud.mmd
        ├── multi-cloud.mmd
        ├── hybrid.mmd
        └── air-gapped.mmd
```

## Writing and Contribution Rules

These are **non-negotiable constraints** for all content in this repository:

1. **Vendor-agnostic** — never reference specific commercial products, cloud providers, or proprietary platforms by name. Use generic terms ("API gateway," "identity provider," "container orchestration platform").
2. **No product names** — no brand names, trademarked tool names, or vendor-specific terminology.
3. **Theoretical and principled** — describe capabilities, patterns, and decision points, not implementation tutorials or step-by-step configuration guides.
4. **Grounded in practice** — guidance must be actionable enough for an architect to translate into concrete decisions.

## File Conventions

- All framework content lives under `docs/`, organized by layer or topic.
- Lowercase filenames with hyphens as separators (e.g., `governance-readiness.md`).
- Diagrams and visual assets go in `assets/`. Mermaid sources use `.mmd` extension.
- One logical topic per file. Split files exceeding ~500 lines.
- Professional, concise, direct tone. Active voice, short paragraphs.
- Prefer bullet points and tables over long prose for structured information.
- Define acronyms on first use within each document.

## Key Design Principles

The framework enforces 10 universal principles (documented in `docs/principles.md`). The most architecturally significant:

- **Separation of Concerns** — each layer has a single, well-defined responsibility
- **Default Deny** — access denied unless explicitly granted
- **Observability by Design** — structured telemetry from day one
- **Version Everything** — policies, prompts, and configs under version control
- **Progressive Disclosure of Complexity** — start simple, add sophistication as maturity grows

## PR Process

- PRs target `main`, require one maintainer review, squash-merge with a descriptive commit message.
- Keep PRs focused on a single concern; discuss large changes in an issue first.
