# Gateway Decision Guide

This document frames the key architectural and operational decisions that an organization must make when designing its gateway layer. Each decision is presented as a choice between well-defined options, with trade-offs articulated so that the decision can be made deliberately rather than by default.

---

## Decision 1 — Deployment Model

**Cloud-managed platform vs. third-party proxy vs. self-hosted**

| Option | Description | Strengths | Weaknesses |
|--------|-------------|-----------|------------|
| **Cloud-managed platform** | A gateway service operated by the organization's cloud provider, running within the provider's managed infrastructure. | Low operational burden; integrates natively with the cloud provider's identity, networking, and monitoring stack; patched and upgraded by the provider. | Limits multi-cloud flexibility; feature set is controlled by the cloud provider's roadmap; may not support all model providers equally. |
| **Third-party proxy** | A gateway product built by an independent vendor, deployed as a managed service or as a self-managed component. | Provider-agnostic by design; typically supports the broadest range of model providers; active feature development driven by a competitive market. | Introduces a third-party dependency; may require a separate licensing or subscription relationship; operational model varies by vendor. |
| **Self-hosted** | A gateway built or assembled in-house, running on the organization's own infrastructure. | Maximum control and customization; no external dependency for feature development; data never leaves the organization's infrastructure. | Highest operational burden; requires dedicated engineering investment for development, patching, and upgrades; risk of the "DIY Trap" anti-pattern. |

**Recommendation:** Start with a cloud-managed or third-party solution to accelerate time-to-value. Reserve self-hosted builds for organizations with extreme regulatory constraints or genuinely unique routing requirements that cannot be met by existing solutions.

---

## Decision 2 — Topology

**Single-gateway vs. regional gateway deployment**

| Option | Description | Strengths | Weaknesses |
|--------|-------------|-----------|------------|
| **Single gateway** | One gateway instance (or cluster) in a single region, serving all workloads regardless of their location. | Simple to operate; single configuration to manage; straightforward cost accounting. | Adds latency for workloads in distant regions; single point of failure at the regional level; may violate data-residency requirements if prompts must not leave a specific geography. |
| **Regional gateways** | A gateway instance in each region where workloads run, connected to a shared control plane for configuration, policy, and reporting. | Low-latency access for all workloads; regional failure isolation; supports data-residency constraints by keeping prompts within the originating region. | Higher operational complexity; configuration must be synchronized across regions; cost tracking must aggregate across instances. |

**Recommendation:** Deploy a single gateway initially. Move to regional gateways when any of the following triggers are met: workload latency exceeds acceptable thresholds due to gateway distance, data-residency regulations require regional confinement, or a regional outage impacts the entire organization.

---

## Decision 3 — Logging Strategy

**Full prompt logging vs. metadata-only vs. tiered by classification**

| Option | Description | Strengths | Weaknesses |
|--------|-------------|-----------|------------|
| **Full prompt logging** | Every request and response — including prompt and completion content — is logged for every request. | Maximum forensic capability; enables model-quality evaluation and prompt-engineering feedback loops at scale. | Stores potentially sensitive data in log infrastructure; increases storage costs; may violate data-minimization regulations. |
| **Metadata-only** | Only metadata (timestamp, team, model, tokens, latency, cost, status) is logged. Prompt and completion content is never persisted. | Minimal data-breach surface in log infrastructure; low storage cost; compliant with strict data-minimization regimes. | Incident investigation is limited to metadata signals; model-quality evaluation requires a separate instrumentation path; prompt-engineering feedback is unavailable. |
| **Tiered by classification** | Logging depth is determined by the request's data-classification tier. Non-sensitive tiers log full content; sensitive tiers log metadata only. | Balances forensic capability with data-minimization obligations; organizations log what they can and protect what they must. | Requires a functioning data-classification mechanism (DLP or request tagging); adds complexity to the logging pipeline; teams must correctly classify their requests. |

**Recommendation:** Implement tiered logging from the start. It avoids the extremes of logging everything (which creates a liability) and logging nothing (which creates a blind spot). If a data-classification mechanism is not yet in place, default to metadata-only and upgrade to tiered logging once classification is operational.

---

## Decision 4 — Cost Model

**Centralized budget vs. per-team budgets with chargebacks**

| Option | Description | Strengths | Weaknesses |
|--------|-------------|-----------|------------|
| **Centralized budget** | A single organizational budget funds all model usage. Teams do not receive individual allocations or invoices. | Simple to administer; removes friction for teams getting started; avoids internal billing disputes. | No team-level accountability for spend; difficult to identify which team is driving cost growth; no natural incentive for teams to optimize usage. |
| **Per-team budgets with chargebacks** | Each team receives a budget allocation. Usage is tracked per team, and costs are charged back to the team's cost center. | Strong accountability; teams self-optimize when they bear the cost; enables per-team budget enforcement at the gateway. | Higher administrative overhead; requires accurate cost attribution; can create friction or slow adoption if budgets are set too conservatively. |

**Recommendation:** Begin with a centralized budget during the pilot phase (fewer than five teams). Transition to per-team budgets with chargebacks as adoption scales. The gateway's per-request cost tagging makes this transition straightforward — the data is already being collected.

---

## Decision 5 — Failover Strategy

**Latency-based vs. cost-based vs. capability-based routing**

| Option | Description | When to use |
|--------|-------------|-------------|
| **Latency-based** | On primary failure, route to the provider with the lowest current latency among healthy alternatives. | Workloads are latency-sensitive (interactive chat, real-time code completion). |
| **Cost-based** | On primary failure, route to the cheapest healthy alternative that meets the minimum capability requirement. | Workloads are cost-sensitive and latency-tolerant (batch summarization, offline embedding generation). |
| **Capability-based** | On primary failure, route to the only alternative that supports the required feature (e.g., function calling, structured output, specific context-window size). | Workloads depend on a feature that is not universally available across providers. |

**Recommendation:** Most organizations need all three strategies, applied to different model aliases. Define the failover strategy per alias in the model registry. Default to latency-based for interactive workloads and cost-based for batch workloads. Use capability-based routing only when a feature constraint eliminates the other options.

---

## Decision 6 — DLP Integration

**Gateway-native pattern matching vs. external classification service**

| Option | Description | Strengths | Weaknesses |
|--------|-------------|-----------|------------|
| **Gateway-native pattern matching** | The gateway runs regex or rule-based pattern matching against outbound prompts using a built-in engine. | Low latency (no external callout); simple to configure; no additional infrastructure dependency. | Limited to syntactic patterns (credit-card numbers, national identifiers, email addresses); cannot classify contextual or semantic sensitive data; high false-positive rate for ambiguous patterns. |
| **External classification service** | The gateway sends prompts to a dedicated classification service (a machine-learning model, a DLP platform, or a custom classifier) via a synchronous API call before forwarding to the provider. | Higher classification accuracy; supports contextual and semantic analysis; can leverage an existing enterprise DLP investment. | Adds latency (one additional network hop plus inference time); introduces a dependency — if the classification service is down, the gateway must fail closed; requires operating and maintaining the classification service. |

**Recommendation:** Start with gateway-native pattern matching for high-confidence syntactic patterns (credit-card numbers, API keys, national identifiers). Run these rules on day one. In parallel, evaluate external classification services for contextual and semantic classification. Integrate the external service as a second-pass check for requests that pass syntactic scanning but are flagged by policy as requiring deeper inspection. This layered approach provides immediate protection while building toward higher-fidelity classification over time.
