# Agent Security Review Checklist

This checklist validates that an autonomous agent --- Layer 5 of the GenAI Adoption Framework --- meets the identity, authorization, audit, and lifecycle requirements necessary for production deployment. Agents introduce risks that standard workloads do not: they reason across multiple steps, invoke external tools, make decisions autonomously, and may delegate to other agents. These capabilities demand a dedicated security review beyond what the Workload Release checklist covers.

Complete every applicable item before granting an agent production access to tools and data. The Workload Release checklist must be complete for the underlying workload; this checklist is an additional gate, not a replacement.

**Owner:** The security architect or designated agent security reviewer, in coordination with the agent's human owner.

**Prerequisite:** [Workload Release Checklist](workload-release.md) --- complete for the agent's underlying workload.

---

## Identity

- [ ] The agent has been registered as a distinct non-human identity in the organization's identity provider.
- [ ] The agent identity is separate from any human user account and from any shared service account used by other systems.
- [ ] A human owner has been assigned to the agent and documented in the identity system; the owner is accountable for the agent's actions.
- [ ] Ownership documentation includes: the agent's purpose, authorized scope, the business justification for autonomous operation, and the conditions under which human intervention is required.
- [ ] The agent identity record includes metadata sufficient for audit: creation date, owning team, associated workload, classification tier, and approval reference.

## Authentication

- [ ] Agent credentials are short-lived or provisioned just-in-time; no long-lived static credentials are assigned to the agent.
- [ ] Credential issuance and rotation are automated; manual credential management is not required for normal operation.
- [ ] If the agent communicates with other agents or services, mutual authentication is configured for all agent-to-agent and agent-to-service interactions.
- [ ] Credential compromise response has been documented: the procedure for revoking and re-issuing agent credentials can be executed within the defined incident response timeline.
- [ ] The agent cannot escalate its own credentials or request additional permissions without going through the governed approval process.

## Authorization

- [ ] Tool-level access control is defined: the agent has an explicit list of tools (APIs, functions, external services) it is authorized to invoke, and all other tools are denied by default.
- [ ] For each authorized tool, the scope of permitted operations is documented (read-only, read-write, specific actions).
- [ ] Parameter-level policies are configured where applicable: the agent is restricted not only in which tools it can invoke but in which parameter values it may supply (e.g., restricted query scopes, prohibited filter values, limited write targets).
- [ ] Default-deny is verified: the agent has been tested with tool invocations outside its authorized set, and all unauthorized invocations are rejected.
- [ ] If the agent can delegate tasks to sub-agents, the delegation policy is defined: which sub-agents may be invoked, what subset of the parent agent's permissions may be delegated, and what restrictions apply.
- [ ] The agent's authorization scope has been reviewed against the principle of least privilege; no tool or parameter access exists that is not required for the agent's defined purpose.

## Audit and Observability

- [ ] The full delegation chain is logged for every action: from the originating human principal, through any intermediate agents, to the final tool invocation.
- [ ] Each log entry in the delegation chain includes: the agent identity, the action taken, the tool invoked, the parameters supplied, the timestamp, and the outcome.
- [ ] Audit logs from the agent are integrated with the organization's security information and event management system (or equivalent centralized security monitoring).
- [ ] The audit trail covers all tool invocations, including those that were denied by authorization policy.
- [ ] Anomaly detection is configured for the agent's behavior patterns: unusual tool invocation frequency, access to tools outside normal operating hours, or invocation of tools that are authorized but rarely used.
- [ ] Audit logs for the agent are retained according to the retention policy defined in governance and cannot be modified or deleted by the agent itself.

## Shadow Agent Discovery

- [ ] A scan for shadow agents has been completed across the organization's infrastructure: any autonomous systems invoking model endpoints or external tools outside the governed process have been identified.
- [ ] Identified shadow agents have been either onboarded through the formal process or decommissioned.
- [ ] Over-privileged agent identities have been identified through access review and remediated (permissions reduced to the minimum required set).
- [ ] A recurring scan cadence has been established to detect new shadow agents (recommended: monthly, or integrated into continuous security monitoring).

## Lifecycle Management

- [ ] An access review schedule has been established for this agent (recommended: quarterly at minimum), during which the agent's tool permissions, classification tier, and business justification are re-evaluated.
- [ ] The decommission process is documented: steps to revoke credentials, remove tool access, archive audit logs, update the identity provider, and notify dependent systems.
- [ ] Decommission criteria are defined: the conditions under which the agent must be taken offline (e.g., human owner departure, workload retirement, security incident, business justification no longer valid).
- [ ] The agent's behavior under graceful shutdown has been tested: when credentials are revoked or tool access is removed, the agent fails safely without corrupting data or leaving tasks in an inconsistent state.
- [ ] Version history of the agent's authorization scope is maintained: every change to the agent's permissions is recorded with the date, approver, and justification.

---

## Completion Criteria

All items in Identity, Authentication, and Authorization must be complete before the agent is granted production tool access. Audit items must be verified before the agent processes any real data. Shadow Agent Discovery and Lifecycle Management items must be addressed before the agent exits its initial probationary period (recommended: 30 days post-deployment).

**Completed by:** ___________________________
**Date:** ___________________________
**Approved by (Security):** ___________________________
**Approved by (Agent Owner):** ___________________________
