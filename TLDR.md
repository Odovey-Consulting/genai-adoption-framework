# GenAI Adoption Framework — TL;DR

A structured, vendor-agnostic framework for adopting generative AI securely, governably, and at scale.

## The Problem

- **Shadow AI is already here.** Teams adopt models ad-hoc, creating ungoverned data flows and compliance exposure.
- **Tooling is fragmented.** Every team wires up its own gateway, logging, and guardrails — or skips them entirely.
- **Costs are ungoverned.** Without centralized routing and observability, token spend is invisible until the invoice arrives.
- **No standard operating model.** Organizations lack a shared vocabulary and layered architecture for running AI in production.

## Five Layers

Each layer builds on the one below it. Deploy them in order.

```
┌─────────────────────────────────┐
│  5. Agent Security              │  Identity, authorization, tool control, audit
├─────────────────────────────────┤
│  4. Workloads                   │  Use-case scoping, model selection, prompt/RAG pipelines
├─────────────────────────────────┤
│  3. Operations                  │  Observability, SecOps, cost management, developer enablement
├─────────────────────────────────┤
│  2. Gateway                     │  Centralized routing, policy enforcement, rate management
├─────────────────────────────────┤
│  1. Governance                  │  Data classification, acceptable use, regulatory mapping, oversight
└─────────────────────────────────┘
```

## Three Maturity Stages

Maturity is assessed **per layer** — not every layer advances at the same pace.

| Stage | Name | What It Means |
|-------|------|---------------|
| 1 | **Foundation** | Minimum viable platform. Core policies defined, a gateway exists, basic observability in place for initial production workloads. |
| 2 | **Operational** | Production-ready. Policy enforcement automated, operations instrumented end-to-end, workloads follow a standardized lifecycle. |
| 3 | **Optimized** | Continuous improvement. Governance adapts dynamically, cost and performance actively tuned, feedback loops connect workload outcomes to platform capabilities. |

## 10 Principles

| # | Principle | In One Line |
|---|-----------|-------------|
| 1 | **Human Accountability** | Every AI action traces back to a named human owner. |
| 2 | **Default Deny** | Access is denied unless explicitly granted through a governed process. |
| 3 | **Defense in Depth** | Security controls exist at every layer; no single point of failure. |
| 4 | **Least Privilege** | Every identity gets the minimum access required for its current task. |
| 5 | **Observability by Design** | Every layer emits structured telemetry from day one, not after an incident. |
| 6 | **Version Everything** | Policies, prompts, configs, and guardrails are version-controlled like code. |
| 7 | **Automate Enforcement, Humanize Judgment** | Machines enforce policy at scale; humans define, tune, and override it. |
| 8 | **Tight Feedback Loops** | Monitoring informs policy; policy changes are immediately reflected in monitoring. |
| 9 | **Separation of Concerns** | Each layer has a single responsibility and does not absorb adjacent ones. |
| 10 | **Progressive Disclosure of Complexity** | Start simple, add sophistication as maturity and requirements demand it. |

## Where to Start

| Role | Entry Point |
|------|-------------|
| **CTO / VP Engineering** | [Framework Overview](docs/overview.md) and [Maturity Model](docs/maturity-model/) — map readiness to milestones. |
| **Platform Engineer** | Layers 1 → 2 → 3, then [Reference Architectures](docs/reference-architectures/) and [Checklists](docs/checklists/). |
| **Security Architect** | [Cross-Cutting Concerns](docs/cross-cutting/) and Layer 5, then review each layer's security controls via [Lenses](docs/lenses/). |
| **Workload Developer** | Start at Layer 4 and work downward as questions arise about platform capabilities or policy constraints. |

## Key Insight

> **Sequence matters.** Governance before Gateway. Gateway before Operations. Operations before Workloads. Workloads before Agent Security. Each layer assumes the one below it is in place. Skip a layer and you build on sand.

---

*For the full framework, start with the [README](README.md).*
