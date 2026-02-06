# Agent Security Best Practices

> **Layer:** 05 — Agent Security
> **Purpose:** Actionable practices that help organizations implement agent security capabilities effectively. Each practice includes guidance, rationale, and the expected outcome when applied consistently.

Practices are organized by concern area. Where a practice directly counters a known failure mode, the corresponding [anti-pattern](anti-patterns.md) is cross-referenced.

---

## Concern Area: Agent Identity

### Practice 1: Register Every Agent in Your Corporate Identity Provider

**Guidance:** Treat agent identities with the same rigor as human or service-account identities. Every agent that will access production tools, data, or systems must be registered in the corporate identity provider alongside human users and service accounts. The registration must include a unique identifier, a named human owner, the agent's declared purpose, and the set of tools it is authorized to invoke. Do not maintain a separate, disconnected registry for agent identities; integrate with the existing identity infrastructure so that security teams have a unified view of all actors in the environment.

**Rationale:** A separate identity silo for agents creates blind spots. Security operations teams monitoring the corporate identity provider for anomalous access patterns will not see agent activity if agents live in a parallel system. Integration ensures that identity policies—password rotation schedules, access-review cadences, offboarding workflows—apply to agents as naturally as they apply to humans. It also enables the permission-inheritance boundary: the identity provider already knows what the owner can access, and agent permissions can be evaluated against that ceiling.

**Expected outcome:** Security teams can answer the question "What agents exist and what can they access?" from the same interface they use for human identities. Agent lifecycle events—creation, modification, suspension, decommissioning—are visible alongside human lifecycle events. Offboarding an employee automatically surfaces the agents they own for reassignment or suspension.

**Counters anti-pattern:** [The Orphan](anti-patterns.md#2-the-orphan) — registration with ownership binding prevents agents from operating without an accountable human.

---

### Practice 2: Use Just-in-Time Credential Injection — Agents Never Hold Secrets

**Guidance:** Design the agent execution platform so that agents never store, retrieve, or manage their own credentials. Instead, the platform injects credentials at the moment of tool invocation, scoped to the specific tool and parameter set the agent is authorized to use. Credentials expire automatically after use or after a short time-to-live (measured in minutes, not hours). Agents should have no mechanism to read, copy, or exfiltrate the credentials they are given; the injection happens at the platform layer, below the agent's application logic.

**Rationale:** Every secret an agent holds is a secret that can be compromised. Agent execution environments—containers, serverless functions, orchestration platforms—are not designed as secure credential stores. Hard-coded keys, long-lived tokens, and persistent secrets in agent configurations are the most common and most exploitable vulnerabilities in agent architectures. Just-in-time injection eliminates the stored-secret attack surface entirely and limits the window of credential validity to the duration of a single operation.

**Expected outcome:** No agent configuration, code repository, or runtime environment contains secrets. Credential compromise requires intercepting a live injection event within its short validity window, dramatically raising the bar for attackers. Credential rotation is seamless because agents do not cache or depend on specific credentials.

**Counters anti-pattern:** [The Zombie](anti-patterns.md#1-the-zombie) — just-in-time injection eliminates the long-lived credentials that create zombie agents.

---

## Concern Area: Authorization

### Practice 3: Implement Tool-Level Access Control Before Parameter-Level Policy

**Guidance:** Begin with coarse-grained tool-level access control: define which agents may invoke which tools, and enforce default-deny semantics so that any tool not explicitly permitted is blocked. Once tool-level controls are stable and operational, layer parameter-level policies on top—constraining not just which tools an agent can call but what it can do with them. Do not attempt to implement parameter-level controls without first having a functioning tool-level boundary. The tool level provides the containment perimeter; the parameter level provides the precision.

**Rationale:** Organizations that attempt to jump directly to parameter-level policy often stall because the complexity is too high to implement across all tools simultaneously. Tool-level control is simpler to reason about, faster to deploy, and provides immediate risk reduction by limiting which tools an agent can reach at all. It also creates the enforcement infrastructure (policy evaluation, invocation interception, logging) that parameter-level policies will build upon. Phasing the rollout ensures each layer is solid before adding complexity.

**Expected outcome:** Every agent in production has an explicit tool-authorization list within the first maturity stage. Parameter-level policies are added incrementally, starting with the highest-risk tools (those that modify data, access sensitive systems, or perform irreversible actions). The organization has a clear, prioritized roadmap for extending parameter-level coverage across the tool surface.

---

### Practice 4: Build the Audit Trail to Your SIEM From Day One

**Guidance:** Integrate agent audit logs with the organization's security information and event management infrastructure from the first production deployment, not as a post-hoc integration. Define the log schema early: agent identifier, tool invoked, parameters passed, delegation chain identifier, timestamp, outcome (success, failure, denied), and owner. Use standard log formats and transport protocols that your SIEM already supports. Ensure delegation chain identifiers propagate across all agent and tool boundaries so that events can be correlated end-to-end.

**Rationale:** Retroactive SIEM integration is one of the most expensive and disruptive tasks in security engineering. If agent logs are generated in a non-standard format, stored in an isolated system, or lack the correlation identifiers needed for chain reconstruction, the migration effort can take months. Building the integration from day one ensures that security operations can monitor agent activity, correlate it with other enterprise events, and investigate incidents using the same tools and workflows they use for all other security data.

**Expected outcome:** Security operations teams can query agent activity in the same SIEM interface they use for all other security events. Correlation between agent actions and infrastructure events (network connections, database queries, API calls) is possible from the first production deployment. Incident investigations involving agents follow the same runbook as other security incidents, with no tooling gaps.

---

## Concern Area: Discovery

### Practice 5: Run Shadow Agent Discovery Before Assuming You Know All Your Agents

**Guidance:** Before declaring the agent registry complete, run a comprehensive discovery sweep of the environment. Analyze API-call patterns for iterative model invocations, multi-tool chains, and automated actions triggered by model outputs. Review network traffic for long-running sessions with tool-calling characteristics. Examine automation platforms, orchestration systems, and CI/CD pipelines for workflows that exhibit agent-like behavior. Treat discovery as a continuous capability, not a one-time exercise: schedule recurring sweeps and maintain detection rules that flag new agent-like patterns as they emerge.

**Rationale:** Teams build autonomous loops without recognizing them as agents. A script that calls a model endpoint in a loop, parses the response, and takes an action is functionally an agent—but the team that built it may not register it because they do not think of it in those terms. Discovery closes the gap between the registry and reality. Organizations that skip discovery and rely solely on voluntary registration consistently undercount their agent population by significant margins, leaving ungoverned agents as blind spots in their security posture.

**Expected outcome:** The organization has a realistic count of agent-like activity in the environment, including registered and unregistered agents. Unregistered agents are classified and either onboarded into the registry with proper identity and authorization controls or terminated. Discovery metrics are reported to the governance board as a leading indicator of agent security posture.

**Counters anti-pattern:** [The Ghost](anti-patterns.md#4-the-ghost) — shadow agent discovery eliminates the blind spot of unregistered autonomous loops.

---

## Concern Area: Governance

### Practice 6: Review Agent Access Quarterly, Same Cadence as Human Access Reviews

**Guidance:** Include agents in the organization's existing periodic access-review process. At the same cadence used for human access reviews—quarterly at minimum—review every registered agent's tool authorizations, parameter-level policies, ownership binding, and lifecycle state. The review should evaluate whether current permissions are still appropriate for the agent's declared purpose, whether the owner is still valid and actively overseeing the agent, and whether any permissions can be revoked based on usage analysis. Use over-privileged identity detection data to focus the review on the highest-risk gaps.

**Rationale:** Permissions granted to agents at registration time drift from actual need as business requirements change, tools are retired, and organizational structures shift. Without periodic review, agents accumulate stale permissions that represent unnecessary risk. Aligning the review cadence with human access reviews ensures that agents are not a blind spot in the access-governance program and leverages the organizational muscle that already exists for periodic review execution.

**Expected outcome:** Agent permissions are evaluated and attested on the same schedule as human permissions. Over-privileged agents are identified and remediated at a predictable cadence. Audit evidence demonstrates that agent access is actively governed, not just initially provisioned.

**Counters anti-pattern:** [The Skeleton Key](anti-patterns.md#3-the-skeleton-key) — periodic review catches agents that have accumulated excessive permissions over time.

---

## Concern Area: Agent-to-Agent Security

### Practice 7: Require Mutual Authentication for All Agent-to-Agent Communication

**Guidance:** Before deploying any multi-agent architecture to production, establish a mutual authentication requirement for all agent-to-agent interactions. Both the initiating agent and the receiving agent must present verifiable identity credentials before data, instructions, or delegation of authority is exchanged. Do not rely on network-level trust (same cluster, same namespace, same VPN) as a substitute for identity-based authentication. Implement credential revocation so that a compromised agent can be immediately excluded from the communication fabric. Log every authentication event—both successful and failed—as part of the delegation chain audit trail.

**Rationale:** Agent-to-agent communication is a trust boundary that is easy to overlook because it often happens within the same platform, the same network, or even the same process. This proximity creates a false sense of security: teams assume that agents on the same platform can be trusted implicitly. In practice, network-level trust does not verify identity, does not prevent impersonation, and does not create the audit trail needed to reconstruct delegation chains. Mutual authentication ensures that every agent-to-agent interaction is explicitly verified and logged, regardless of network topology.

**Expected outcome:** Every agent-to-agent interaction in production is mutually authenticated. Security operations can trace the identity of both parties in any agent-to-agent communication event. Compromised agents can be immediately excluded from multi-agent workflows by revoking their credentials without disrupting other agents.

**Counters anti-pattern:** [The Trust Fall](anti-patterns.md#6-the-trust-fall) — mutual authentication replaces implicit trust with verified identity in agent-to-agent communication.
