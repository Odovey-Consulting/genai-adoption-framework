# Agent Security Decision Guide

> **Layer:** 05 — Agent Security
> **Purpose:** Provide structured guidance for the key architectural and operational decisions organizations face when implementing agent security controls. Each decision includes context, options, evaluation criteria, and trade-offs.

The decisions below arise in roughly the order organizations encounter them as agent adoption matures. Earlier decisions constrain later options, so revisiting this guide at each maturity transition is recommended.

---

## Decision 1: Agent Identity Model

**Context:** Agents need identities to support authentication, authorization, and audit. The organization must decide how to represent agent identities within its existing identity infrastructure.

**Options:**

| Option | Description |
|--------|-------------|
| **Extend corporate identity provider** | Create agent identities as a new object type (or a variant of service accounts) within the existing corporate identity provider. Agents are managed alongside human users and service accounts in the same directory, same administrative interface, same lifecycle workflows. |
| **Dedicated agent identity platform** | Deploy a purpose-built identity system specifically for agents, separate from the corporate identity provider. The agent identity platform has its own directory, its own lifecycle management, and its own administrative interface. |

**Evaluation criteria:**
- **Unified visibility:** Can security operations see all actors—human, service, and agent—in a single view?
- **Policy consistency:** Do identity lifecycle policies (rotation, review, offboarding) apply uniformly across identity types?
- **Feature fit:** Does the corporate identity provider support the agent-specific attributes needed (tool authorizations, parameter-level scopes, delegation chain metadata)?
- **Operational burden:** Does the team have the expertise to extend the corporate identity provider, or is a separate platform faster to deploy?

**Trade-offs:** Extending the corporate identity provider delivers unified visibility and policy consistency but may require significant configuration to support agent-specific attributes that the provider was not designed for. A dedicated platform offers purpose-built features but creates an identity silo that security operations must monitor separately, and lifecycle policies must be duplicated or synchronized. Most organizations should start by extending the corporate identity provider and only introduce a dedicated platform if the provider's extensibility proves genuinely insufficient.

---

## Decision 2: Authorization Granularity

**Context:** Agent authorization can be enforced at different levels of specificity. The organization must decide how granular its authorization policies will be, recognizing that greater granularity provides stronger security but increases policy-management complexity.

**Options:**

| Option | Description |
|--------|-------------|
| **Tool-level only** | Authorization policies define which tools each agent may invoke, using default-deny semantics. No constraints are placed on the parameter values the agent passes to authorized tools. |
| **Tool + parameter-level** | Authorization policies define both which tools each agent may invoke and what parameter values are permitted for each tool. Parameter constraints may include allowed value ranges, permitted patterns, scope restrictions, and forbidden values. |

**Evaluation criteria:**
- **Risk profile of accessible tools:** Do the tools agents invoke perform read-only queries, or do they modify data, send communications, or execute transactions?
- **Sensitivity of parameter values:** Do parameters contain or reference sensitive data (customer identifiers, financial amounts, recipient addresses)?
- **Policy management capacity:** Does the team have the tooling and expertise to write, test, and maintain parameter-level policies at scale?
- **Incremental deployability:** Can parameter-level policies be added incrementally to the highest-risk tools first?

**Trade-offs:** Tool-level-only authorization is simpler to implement and manage but leaves the parameter space uncontrolled—an agent authorized to query a database can query any table. Tool + parameter-level authorization provides true least privilege but requires significantly more policy authoring, testing, and maintenance. The recommended approach is to implement tool-level controls universally and then layer parameter-level policies onto the highest-risk tools first, expanding coverage incrementally based on risk.

---

## Decision 3: Policy Engine

**Context:** Agent authorization policies must be evaluated at runtime, at each tool invocation. The organization must decide what type of policy engine will perform this evaluation.

**Options:**

| Option | Description |
|--------|-------------|
| **Purpose-built agent policy engine** | A policy engine designed specifically for agent authorization scenarios, with native support for tool-level and parameter-level policies, delegation chain evaluation, and agent-specific attributes. |
| **General-purpose policy engine** | An existing enterprise policy engine (typically used for API authorization, network policy, or infrastructure access control) extended to handle agent authorization use cases. |

**Evaluation criteria:**
- **Delegation chain support:** Does the engine natively evaluate delegation chains, or must this logic be built on top?
- **Parameter-level expressiveness:** Can the engine express parameter-value constraints with sufficient precision (ranges, patterns, conditional logic)?
- **Organizational expertise:** Does the team already operate and understand a general-purpose policy engine?
- **Integration cost:** How much effort is required to integrate the engine with the agent execution platform and tool-invocation layer?

**Trade-offs:** A purpose-built agent policy engine offers native support for agent-specific concepts (delegation chains, tool-parameter hierarchies, agent lifecycle states) but introduces a new system to operate and maintain. A general-purpose engine leverages existing operational expertise and infrastructure but may require significant extension to support agent-specific policy patterns, particularly delegation chain evaluation. Organizations with an established, extensible policy engine should attempt to extend it first; those without should evaluate purpose-built options to avoid building agent-specific policy logic from scratch.

---

## Decision 4: Credential Management

**Context:** Agents need credentials to authenticate when invoking tools. The organization must decide how credentials are delivered to agents and how their lifecycle is managed.

**Options:**

| Option | Description |
|--------|-------------|
| **Gateway-injected** | A gateway or proxy that sits between the agent and the tool server injects credentials into the request at invocation time. The agent never sees or handles the credential; it sends an unauthenticated request, and the gateway adds the appropriate credential before forwarding to the tool server. |
| **Vault-integrated** | The agent retrieves credentials from a centralized secrets vault at invocation time, using a short-lived token to authenticate to the vault. The agent receives the credential, uses it for the tool invocation, and the credential expires after use. |
| **Short-lived tokens** | The agent receives a short-lived token (scoped to the specific tool and operation) from a token-issuance service at invocation time. The token is passed directly to the tool server, which validates it against the issuance service. |

**Evaluation criteria:**
- **Agent exposure to secrets:** Does the agent ever hold a usable credential in its memory or execution environment?
- **Architectural complexity:** How many components are involved in the credential-delivery path?
- **Tool server compatibility:** Does the tool server support the credential format delivered by each approach?
- **Revocation speed:** How quickly can a credential be revoked if a compromise is detected?

**Trade-offs:** Gateway-injected credentials provide the strongest isolation—the agent never touches a secret—but require all tool invocations to route through the gateway, which may introduce latency and a single point of failure. Vault-integrated credentials are flexible and compatible with most tool servers but briefly expose the credential to the agent's execution environment. Short-lived tokens avoid persistent secrets and support direct agent-to-tool communication but require tool servers to integrate with the token-validation service. Organizations should prefer gateway injection for the highest-sensitivity tools and use short-lived tokens for lower-sensitivity invocations where gateway routing is impractical.

---

## Decision 5: Audit Depth

**Context:** Agent audit trails can capture varying levels of detail. The organization must decide how much information to log for each agent action, balancing investigative value against storage cost, privacy considerations, and performance impact.

**Options:**

| Option | Description |
|--------|-------------|
| **Metadata-only** | Log the agent identifier, tool invoked, timestamp, delegation chain identifier, and outcome (success/failure/denied). Do not log the parameter values passed to the tool or the data returned. |
| **Full parameter logging** | Log everything in metadata-only, plus the complete parameter set passed to each tool invocation and, optionally, a summary or hash of the response. |

**Evaluation criteria:**
- **Investigative sufficiency:** Can security operations reconstruct what happened in an incident using metadata alone, or are parameter values essential?
- **Data sensitivity:** Do parameter values contain personally identifiable information, financial data, or other regulated content that creates additional compliance obligations when logged?
- **Storage and cost:** What is the volume of agent invocations, and what is the cost of storing full parameter data at that volume?
- **Retention requirements:** Do regulatory or contractual obligations specify what must be logged and for how long?

**Trade-offs:** Metadata-only logging is lightweight, avoids logging sensitive parameter data, and is sufficient for most operational monitoring. However, it cannot answer the question "What exactly did the agent do?" during incident investigation—only that it invoked a tool at a time. Full parameter logging provides complete forensic capability but significantly increases storage costs, may capture sensitive data that itself requires protection, and may require data-masking or redaction capabilities to comply with privacy regulations. Most organizations should implement full parameter logging for high-risk tools (those that modify data, access sensitive systems, or perform financial transactions) and metadata-only logging for lower-risk, high-volume tools. Ensure that logged parameter data is subject to the same classification and access controls as the underlying data it references.

---

## Decision 6: Discovery Approach

**Context:** Organizations must detect agent-like activity that is not associated with a registered agent identity. The organization must decide how to discover shadow agents in the environment.

**Options:**

| Option | Description |
|--------|-------------|
| **Passive scanning** | Monitor network traffic, API logs, and model-endpoint access patterns for behavioral signatures consistent with agent activity (iterative model calls, multi-tool chains, automated actions). Discovery operates on existing telemetry without requiring changes to the environment. |
| **Active enumeration** | Proactively query automation platforms, orchestration systems, CI/CD pipelines, scheduled-task systems, and compute environments for running processes that exhibit agent characteristics. Discovery involves direct inspection of the runtime environment. |
| **Mandatory registration with enforcement** | Require all agent workloads to register before deployment and enforce this through technical controls (e.g., model endpoints reject requests from unregistered identities, tool servers require registered agent credentials). Discovery is reduced to detecting circumvention of registration controls. |

**Evaluation criteria:**
- **Coverage:** How likely is each approach to find the full population of shadow agents, including those on unmanaged infrastructure?
- **Intrusiveness:** Does the approach require changes to existing infrastructure or workflows?
- **Timeliness:** How quickly does the approach detect a new shadow agent after it begins operating?
- **Sustainability:** Can the approach run continuously at an acceptable operational cost?

**Trade-offs:** Passive scanning is non-intrusive and can run continuously but depends on the quality of existing telemetry and may miss agents that do not generate distinctive network patterns. Active enumeration provides higher confidence but requires access to compute environments and may not cover unmanaged infrastructure. Mandatory registration with enforcement is the strongest control but is only feasible when the organization controls all model endpoints and tool servers—which is rarely true in early maturity stages. The recommended approach is to layer all three: mandatory registration as the primary control, passive scanning as the continuous backstop, and periodic active enumeration as the high-confidence sweep. Start with passive scanning in the Operational stage, add mandatory registration as platform maturity allows, and introduce active enumeration at the Optimized stage.
