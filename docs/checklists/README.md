# Checklists

## Purpose

The GenAI Adoption Framework defines five stacking layers, each building on the one below it. The checklists in this section provide stage-gate criteria for each layer --- a structured set of verification items that an organization must satisfy before considering a layer ready for production use.

Checklists translate the framework's architectural guidance into concrete, binary questions. Every item is either done or not done. There is no partial credit. This deliberate rigidity serves a purpose: it forces teams to confront gaps that narrative documentation can obscure. A layer document might describe a capability in aspirational terms; the corresponding checklist demands evidence that the capability actually exists.

## How to Use These Checklists

**Pre-launch verification.** Before declaring a layer operational, the responsible team walks through every item in the corresponding checklist. Items that cannot be checked off represent unresolved risks that must be either addressed or formally accepted with documented justification.

**Go/no-go decisions.** Each checklist serves as the input to a go/no-go decision gate. A governance review board, platform team lead, or security architect (depending on the layer) reviews the completed checklist and either approves the layer for production use or identifies the items that block approval.

**Living documents.** Checklists should be revisited as the organization matures. An item that was not applicable during the Foundation stage may become critical at the Operational stage. Teams should version their completed checklists alongside other governance artifacts.

**Audit evidence.** A completed checklist, dated and signed by the responsible owner, serves as lightweight audit evidence that due diligence was performed before a layer went live.

## One Checklist Per Layer

Each checklist corresponds to one of the five framework layers:

| Checklist | Layer | Purpose |
|-----------|-------|---------|
| [Governance Readiness](governance-readiness.md) | Layer 1: Governance | Verify that policies, processes, and cost structures are defined before enforcement begins |
| [Gateway Deployment](gateway-deployment.md) | Layer 2: Gateway | Verify that the centralized control point is properly deployed, secured, and configured |
| [Operations Launch](operations-launch.md) | Layer 3: Operations | Verify that observability, security operations, and developer enablement are production-ready |
| [Workload Release](workload-release.md) | Layer 4: Workloads | Verify that an individual workload meets quality, security, and operational standards before production |
| [Agent Security Review](agent-security-review.md) | Layer 5: Agent Security | Verify that an autonomous agent meets identity, authorization, and audit requirements |

The checklists follow the stacking order intentionally. Completing the Governance Readiness checklist is a prerequisite for the Gateway Deployment checklist. Gateway Deployment is a prerequisite for Operations Launch. This sequencing mirrors the framework's core architectural principle: each layer depends on the one below it.

---

*Navigate back to the [Framework Overview](../overview.md) or the [Maturity Model](../maturity-model/) for context on when each checklist becomes relevant.*
