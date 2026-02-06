# Agent Security Capabilities

> **Layer:** 05 — Agent Security
> **Purpose:** Enumerate the capabilities an organization must build or acquire to secure autonomous agents operating in the enterprise. Each capability is mapped to the maturity stage at which it becomes critical.

Capabilities are grouped by functional area. For each capability, the entry specifies what it is, why it is required, and the maturity stage at which the organization cannot proceed without it. Maturity stages referenced throughout are: **Crawl** (first agents in sandboxed environments), **Walk** (agents operating in production with real data and tool access), **Run** (enterprise-wide agent deployment with agent-to-agent communication and complex delegation chains).

---

## Functional Area: Identity

### 1. Agent Registry

**Description:** A centralized, authoritative inventory of all autonomous agents operating in the environment. Each registry entry records the agent's unique identifier, human owner, declared purpose, approved tool set, parameter-level authorization scope, current lifecycle state (development, staging, production, suspended, decommissioned), creation date, and last-review date.

**Why it is required:** The agent registry is the foundation on which every other agent security capability depends. Without a registry, the organization has no definitive answer to the question "What agents exist?" Discovery can find shadow agents, but only the registry provides the governed, attested source of truth. Authorization policies reference registry entries to determine what an agent may do. Audit trails reference registry entries to attribute actions. Lifecycle management operates on registry entries to control state transitions.

**Critical maturity stage:** **Crawl.** The registry must exist before the first agent is deployed beyond a local development environment. Even a single-agent pilot must be registered so that identity, ownership, and access controls are established from the outset.

**Key outputs:** Searchable registry with mandatory fields (owner, purpose, tool scope, lifecycle state), API for programmatic registration and query, integration with lifecycle-management workflows, dashboard for registry completeness metrics.

---

### 2. Agent-to-Human Ownership Binding

**Description:** A mandatory, enforced linkage between every registered agent and a named individual who serves as the agent's accountable owner. The binding is not a soft reference; changes to the owner's employment status, role, or access automatically trigger workflows that re-evaluate the agent's permissions and ownership.

**Why it is required:** Ownership binding operationalizes the principle that [every agent has a human owner](principles.md#1-every-agent-has-a-human-owner). Without an enforced binding, ownership degrades into a metadata field that is populated at registration and never updated. When the binding is enforced, the organization can guarantee that every agent in production has a current, valid, reachable owner—and that ownership changes propagate to access-control and lifecycle decisions.

**Critical maturity stage:** **Crawl.** Ownership binding must be enforced from the first agent registration. Deferring it creates orphaned agents that are difficult to remediate retroactively.

**Key outputs:** Ownership field validated against the corporate identity directory at registration and on a recurring schedule, automated alerts when owner changes are detected (departure, role change), grace-period enforcement with automatic agent suspension for unresolved ownership gaps.

---

### 3. Identity Lifecycle Management

**Description:** A defined set of states and transitions that govern an agent's lifecycle from creation through decommissioning: development, staging, production, suspended, and decommissioned. Each transition has defined criteria, required approvals, and audit records.

**Why it is required:** Agents that are not subject to lifecycle management accumulate in production indefinitely, even when their purpose is obsolete, their owner has changed, or their access scope is no longer appropriate. Lifecycle management ensures that agents are reviewed before entering production, can be suspended rapidly in response to security events, and are decommissioned cleanly when no longer needed—including revocation of all credentials and removal of all access grants.

**Critical maturity stage:** **Walk.** During Crawl, agents are few enough that lifecycle can be managed informally. As the agent population grows in Walk, formal state management with enforced transitions becomes essential.

**Key outputs:** State machine definition with transition criteria and approval requirements, automated enforcement of transition rules, suspension capability with sub-minute activation time, decommissioning workflow that includes credential revocation, access-grant cleanup, and registry archival.

---

### 4. Credential Issuance with Expiration and Rotation

**Description:** A credential-management capability that issues short-lived, scoped credentials to agents at the time of tool invocation, automatically expires them after use or timeout, and supports emergency rotation when a compromise is suspected.

**Why it is required:** This capability operationalizes the principle of [no standing access](principles.md#4-no-standing-access). Long-lived credentials stored in agent environments are a high-value target; their compromise grants persistent access that may go undetected. Just-in-time issuance with aggressive expiration windows limits the blast radius of a credential compromise to a single operation, and rotation capability ensures that suspected compromises can be contained immediately.

**Critical maturity stage:** **Walk.** During Crawl in sandboxed environments, credential management can be simplified. Once agents access production systems with real data in Walk, just-in-time credential issuance with expiration becomes a hard requirement.

**Key outputs:** Credential-issuance API integrated with the agent execution platform, configurable time-to-live with maximum enforced ceiling, automatic revocation on task completion, emergency rotation procedures, issuance and revocation audit logs feeding the delegation chain.

---

## Functional Area: Authentication

### 5. Agent Authentication at the Tool Access Layer

**Description:** A mechanism that verifies an agent's identity each time it invokes a tool, API, or external service. Authentication occurs at the point of tool access, not at session establishment, ensuring that every individual invocation is attributable to a specific, verified agent identity.

**Why it is required:** Session-based authentication allows an agent to authenticate once and then make an unbounded number of tool calls under that session. If the session is hijacked or the agent's behavior deviates mid-session, individual invocations cannot be independently verified. Per-invocation authentication, combined with short-lived credentials, ensures that every tool call is independently attributable and authorized.

**Critical maturity stage:** **Walk.** Per-invocation authentication adds overhead that may be deferred during sandboxed Crawl experiments. In Walk, where agents access production tools, per-invocation authentication is required to maintain traceability and limit exposure.

**Key outputs:** Authentication middleware or gateway that intercepts tool invocations, credential-verification logic per invocation, rejection and alerting for failed authentication attempts, integration with the credential-issuance system.

---

### 6. Mutual Authentication for Agent-to-Agent Communication

**Description:** A protocol-level mechanism that requires both the initiating and receiving agents to authenticate their identities before any data, instructions, or delegation of authority is exchanged between them.

**Why it is required:** Agent-to-agent communication creates trust relationships that are invisible to human operators unless explicitly authenticated and logged. Without mutual authentication, an agent accepting a delegation request has no cryptographic assurance that the requesting agent is who it claims to be. This opens the door to impersonation, unauthorized delegation, and injection of malicious instructions into multi-agent workflows.

**Critical maturity stage:** **Run.** Agent-to-agent communication typically emerges at scale when organizations build multi-agent architectures. Mutual authentication must be in place before agent-to-agent patterns are approved for production use.

**Key outputs:** Mutual authentication protocol integrated with the agent communication framework, certificate or token exchange at the start of each agent-to-agent interaction, failed-authentication alerting, integration with delegation-chain tracking.

---

### 7. Identity Provider Integration

**Description:** Integration of the agent identity system with the organization's existing identity provider infrastructure. Agent identities are managed alongside human and service-account identities, subject to the same lifecycle policies, and visible in the same administrative interfaces.

**Why it is required:** A separate, siloed identity system for agents creates blind spots in the organization's overall identity posture. Security teams cannot correlate agent activity with human activity if identities live in different systems. Integration with the corporate identity provider also enables the permission-inheritance boundary: the owner's entitlements in the identity provider serve as the ceiling for the agent's permissions.

**Critical maturity stage:** **Walk.** During Crawl, a standalone agent registry may suffice. In Walk, when agents access the same production systems as human users, integration with the corporate identity provider becomes necessary for consistent enforcement and visibility.

**Key outputs:** Agent identities represented in the corporate identity directory, synchronized lifecycle events (owner departure triggers agent review), unified administrative view of human and agent identities, identity-provider-based policy evaluation for agent authorization decisions.

---

## Functional Area: Authorization

### 8. Tool-Level Access Control

**Description:** A policy engine that determines which tools each agent is authorized to invoke, based on the agent's registered purpose, its owner's entitlements, and the principle of least privilege. Default-deny semantics apply: an agent may invoke only tools explicitly listed in its authorization policy.

**Why it is required:** Without tool-level access control, any registered agent can invoke any tool the platform exposes. This violates least privilege and creates an attack surface where a compromised or misbehaving agent can reach systems far beyond its intended scope. Tool-level access control is the coarse-grained authorization boundary that limits the agent's operational footprint.

**Critical maturity stage:** **Crawl.** Even the first agent must have an explicit tool authorization list. Deploying an agent with unrestricted tool access, even in a sandbox, establishes a dangerous precedent.

**Key outputs:** Policy definitions mapping agents to permitted tools, default-deny enforcement, policy evaluation at each tool invocation, denied-invocation logging and alerting.

---

### 9. Parameter-Level Policy Enforcement

**Description:** Fine-grained authorization policies that constrain not only which tools an agent may invoke but also the parameter values it may pass. Policies define permitted values, ranges, patterns, or scopes for each parameter of each authorized tool invocation.

**Why it is required:** Tool-level authorization answers "Can this agent call this tool?" but leaves the equally important question "What can this agent do with this tool?" unaddressed. An agent authorized to query a database can query any table unless the query parameters are constrained. Parameter-level enforcement closes this gap by extending least privilege into the invocation itself, ensuring the agent's effective permissions match its intended purpose.

**Critical maturity stage:** **Walk.** During Crawl, tool-level controls may suffice for simple, single-tool agents. As agents access production data and multi-tool workflows emerge in Walk, parameter-level enforcement becomes critical to prevent over-privileged tool usage.

**Key outputs:** Parameter-constraint definitions per tool per agent, enforcement engine that evaluates parameter values against policy before the invocation reaches the downstream system, violation logging with full context, policy templates for common tool patterns.

---

### 10. Delegation Chain Enforcement

**Description:** Authorization logic that evaluates not only the agent's own permissions but also the delegation chain through which the request originated. An agent may invoke a tool only if every link in the delegation chain—from the originating human to the current agent—has sufficient authority for the requested action.

**Why it is required:** In multi-agent architectures, an agent may receive a delegation from another agent that itself received a delegation from a human user. Without delegation-chain enforcement, the intermediate agent could delegate authority it does not possess, or the final agent could act on a delegation whose originator has been revoked. Evaluating the full chain at each invocation point ensures that delegated authority is valid end-to-end.

**Critical maturity stage:** **Run.** Delegation-chain enforcement is required when multi-agent workflows and agent-to-agent delegation patterns are deployed in production. Single-agent architectures in Crawl and Walk may defer this capability.

**Key outputs:** Chain-evaluation logic at each authorization decision point, chain-integrity validation (no missing links), revocation propagation (revoking a link invalidates the entire downstream chain), chain-evaluation audit records.

---

## Functional Area: Audit

### 11. Full Delegation Chain Audit Trail

**Description:** A comprehensive, structured log that captures every event in an agent's execution—tool invocations, parameter values, delegation events, decision branches, errors, and retries—linked together by the delegation chain that connects them to the originating human action.

**Why it is required:** The audit trail is the evidentiary foundation for incident investigation, compliance reporting, and forensic analysis. Without it, the organization cannot reconstruct what an agent did, why it did it, or who authorized it. The trail must be complete (no gaps), immutable (no retroactive modification), and integrated with the organization's security-event infrastructure to enable correlation with other enterprise security data.

**Critical maturity stage:** **Walk.** Basic logging may suffice in Crawl. In Walk, when agents act on production data, a complete, structured, SIEM-integrated audit trail is a hard requirement for security operations and compliance.

**Key outputs:** Structured log entries with standard schema (agent ID, tool, parameters, delegation chain ID, timestamp, outcome), tamper-evident storage, SIEM integration via standard formats and transport protocols, query and visualization interface for investigation.

---

### 12. Delegation Chain Logging

**Description:** Dedicated logging of delegation events—when authority is passed from a human to an agent, from one agent to another, or from an agent to a tool—capturing the delegating party, receiving party, scope of delegated authority, and timestamp.

**Why it is required:** Delegation events are the backbone of accountability in multi-agent systems. While the full audit trail captures all agent activity, delegation logging specifically tracks the flow of authority. This enables security operations to answer the critical question: "Who authorized this agent to take this action?" It also supports compliance requirements for demonstrating that all automated actions trace to a human authorization.

**Critical maturity stage:** **Walk.** Delegation logging must be in place when agents move to production. For multi-agent delegation patterns in Run, the logging must support chain reconstruction across agent boundaries.

**Key outputs:** Delegation event log with standard schema, chain-reconstruction query capability, integration with the full audit trail, alerting on anomalous delegation patterns (unexpected delegators, scope escalation).

---

## Functional Area: Discovery

### 13. Shadow Agent Detection

**Description:** Continuous monitoring capability that identifies autonomous agent-like behavior in the environment that is not associated with a registered agent identity. Detection methods include analysis of API-call patterns (iterative model invocations, multi-tool chains), network-traffic analysis, behavioral anomaly detection, and correlation of unregistered identities with tool-invocation logs.

**Why it is required:** Registration-based controls only govern agents that are voluntarily registered. Teams inevitably build autonomous loops—scripts, workflows, automations—without recognizing them as agents. Shadow agent detection closes the gap between the governed (registered) agent population and the actual agent population, ensuring the organization's security posture reflects reality rather than the registry.

**Critical maturity stage:** **Walk.** During Crawl, the agent population is small enough that manual tracking may suffice. In Walk, as more teams adopt agent patterns, the probability of unregistered agents increases significantly.

**Key outputs:** Detection rules for agent-like behavioral patterns, automated alerting when unregistered agent activity is detected, investigation workflow for classifying and remediating shadow agents, metrics dashboard (shadow agents detected, time to remediation, recurrence rate).

---

### 14. Over-Privileged Identity Detection

**Description:** Periodic and continuous analysis of agent authorization policies to identify agents with permissions that exceed what their declared purpose requires. Detection compares the agent's authorized tool and parameter scope against its actual usage patterns, flagging unused authorizations and permissions that significantly exceed observed behavior.

**Why it is required:** Permissions accumulate over time as agents are granted access for specific tasks and those grants are never revoked. Over-privileged agents represent an outsized risk because their blast radius in a compromise scenario exceeds their operational footprint. Detecting and remediating over-privilege is a core practice of least-privilege enforcement at scale.

**Critical maturity stage:** **Run.** During Crawl and Walk, agent populations are small enough for manual review. At Run scale, automated detection becomes necessary to maintain least privilege across a large agent population.

**Key outputs:** Usage-versus-authorization comparison reports, automated alerts for significant permission-usage gaps, remediation recommendations (specific permissions to revoke), integration with periodic access-review workflows.

---

### 15. Tool Server Discovery

**Description:** Continuous or periodic enumeration of all tool servers, APIs, and external services that agents can potentially reach within the network environment. Discovery identifies both sanctioned tool endpoints (those registered in the platform's tool catalog) and unsanctioned endpoints (those reachable but not governed).

**Why it is required:** Agent authorization policies are only effective if the organization knows the full set of tools available for invocation. An agent that can reach an unsanctioned tool endpoint bypasses tool-level access controls entirely. Tool server discovery maps the actual tool surface, enabling the security team to either bring unsanctioned endpoints under management or block agent access to them.

**Critical maturity stage:** **Run.** During Crawl and Walk, the tool surface is typically small and manually managed. At Run scale, tool endpoints proliferate across teams, and automated discovery becomes necessary to maintain a complete inventory.

**Key outputs:** Inventory of all tool endpoints reachable by agents, classification of each as sanctioned or unsanctioned, network segmentation recommendations for isolating unsanctioned endpoints, integration with tool-level access-control policies.

---

## Functional Area: Governance

### 16. Periodic Access Reviews

**Description:** Scheduled reviews of agent authorization policies on the same cadence as human access reviews (quarterly at minimum). Each review evaluates whether the agent's current tool and parameter authorizations remain appropriate for its declared purpose, whether the owner is still valid, and whether the agent should continue in its current lifecycle state.

**Why it is required:** Access reviews are the catch-all control that compensates for the inevitable drift between granted permissions and actual need. Without periodic reviews, agents accumulate stale permissions, outdated ownership bindings, and lifecycle states that no longer reflect reality. Aligning the review cadence with human access reviews ensures agents are not a blind spot in the organization's broader access-governance program.

**Critical maturity stage:** **Walk.** Formal periodic reviews are required once agents access production systems. During Crawl, ad-hoc review is acceptable.

**Key outputs:** Review schedule aligned with human access-review cadence, review checklist (permissions, ownership, lifecycle state, usage patterns), attestation records per agent per review cycle, remediation tracking for identified gaps.

---

### 17. Compliance Reporting for Agent Actions

**Description:** Reporting capability that aggregates agent audit data into compliance-oriented views, demonstrating that agent actions conform to organizational policies, regulatory requirements, and contractual obligations. Reports cover delegation-chain integrity, authorization-policy adherence, credential-management compliance, and shadow-agent remediation metrics.

**Why it is required:** Regulatory frameworks increasingly require organizations to demonstrate that automated systems—including AI agents—operate within defined boundaries and are subject to human oversight. Compliance reporting translates raw audit data into evidence that auditors, regulators, and internal governance boards can evaluate. Without this capability, the organization may be fully compliant but unable to prove it.

**Critical maturity stage:** **Run.** During Walk, compliance evidence can be assembled manually from audit logs. At Run scale, automated reporting is required to meet the volume and frequency of compliance obligations.

**Key outputs:** Pre-built compliance report templates for common regulatory frameworks, scheduled report generation, on-demand report generation for audits and investigations, executive dashboard summarizing agent compliance posture.
