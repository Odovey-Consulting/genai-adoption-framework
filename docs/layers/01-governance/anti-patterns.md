# Governance Anti-Patterns

> **Layer:** 01 — Governance
> **Purpose:** Document common failure modes in generative-AI governance so organizations can recognize and avoid them. Each anti-pattern is linked to the best practice or principle that corrects it.

Anti-patterns are recurring approaches that appear reasonable on the surface but produce poor outcomes at scale. Recognizing them early saves significant remediation effort.

---

## 1. The Wild West

**Description:** Teams acquire API keys, provision model endpoints, and deploy generative-AI features with no centralized governance. There is no acceptable use policy, no model catalog, and no data-classification scheme. Each team makes independent decisions about which models to use, what data to send, and how to handle outputs.

**Symptoms:**
- Finance discovers significant, unexplained charges from multiple model-provider accounts that no single team claims ownership of.
- Security audit reveals dozens of active API keys scattered across team repositories, many with overly broad permissions and no rotation schedule.
- Different teams use different models for similar tasks with no shared evaluation criteria, producing inconsistent quality and duplicated spend.

**Consequences:**
- Sensitive data is sent to external model endpoints without appropriate contractual or technical safeguards, creating legal and regulatory exposure.
- Costs accumulate without attribution, making budgeting impossible and triggering reactive, disruptive cost-cutting measures.
- When an incident occurs, there is no policy to reference, no owner to contact, and no escalation path to follow.

**Correct approach:** Establish the [Acceptable Use Policy](best-practices.md#practice-1-write-the-aup-in-plain-language-developers-actually-read) and [Model Catalog](best-practices.md#practice-3-start-with-a-conservative-model-catalog-and-expand-deliberately) before provisioning any production access. Apply the principle of [Policy Before Technology](principles.md#1-policy-before-technology).

---

## 2. The Ivory Tower

**Description:** Governance is so restrictive, slow, or disconnected from operational reality that teams cannot use generative AI for legitimate, low-risk use cases. Approval processes take weeks or months. The acceptable use policy prohibits broad categories of use without risk-proportionate justification. The review board meets infrequently and lacks representation from the teams doing the work.

**Symptoms:**
- Use case approval queues grow continuously, with average approval times exceeding the sprint cycles of requesting teams.
- Teams begin routing requests through informal channels or reframing generative-AI use cases as "traditional ML" to avoid the governance process.
- The governance board has no active practitioners among its members; decisions are made by people who have never built a generative-AI feature.

**Consequences:**
- Legitimate adoption stalls, and the organization falls behind peers in realizing productivity and quality gains from generative AI.
- Shadow adoption emerges: teams use personal accounts or ungoverned tools to accomplish work the official platform will not allow, creating precisely the ungoverned risk the policies were designed to prevent.
- Governance loses credibility, making it harder to enforce even the rules that genuinely matter.

**Correct approach:** [Build the review board with cross-functional representation](best-practices.md#practice-6-build-the-review-board-with-cross-functional-representation) that includes active practitioners. Implement an [exemption process](capabilities.md#2-exemption-request-and-approval-process) with published SLAs. Design policies that are proportionate to risk, not uniformly restrictive.

---

## 3. The Paper Tiger

**Description:** Governance policies exist on paper—often comprehensive and well-written—but nothing enforces them. There is no technical enforcement at the gateway layer, no monitoring for violations, and no consequence when policies are breached. The policies are treated as a compliance checkbox rather than an operational control.

**Symptoms:**
- The AUP was published months ago, but no team has ever been asked to demonstrate compliance or received feedback on a violation.
- Gateway and orchestration layers have no rules that reference the AUP; requests flow through without classification checks, PII scanning, or usage-limit enforcement.
- When asked, developers acknowledge the AUP exists but cannot name its key provisions.

**Consequences:**
- A false sense of security prevails: leadership believes governance is in place because documents exist, while actual behavior is ungoverned.
- When an incident occurs, the organization discovers it has no operational muscle for enforcement—policies must be operationalized under crisis conditions.
- Regulatory auditors identify the gap between documented policy and actual practice, resulting in findings that are more damaging than having no policy at all (because the gap suggests awareness without action).

**Correct approach:** [Map every AUP rule to a gateway enforcement mechanism](best-practices.md#practice-2-map-every-aup-rule-to-a-gateway-enforcement-mechanism). For rules that cannot yet be enforced technically, assign manual monitoring owners and track enforcement gaps in the governance backlog.

---

## 4. The Moving Target

**Description:** Governance policies, classification rules, and approval criteria change frequently and without sufficient communication. Teams invest effort to comply with one version of the rules, only to discover the rules have shifted by the time their implementation is complete. Changes are made without a release process, versioning, or advance notice.

**Symptoms:**
- Teams cite conflicting versions of the same policy, each believing theirs is current.
- The governance repository (or wiki, or shared drive) shows frequent edits with no changelog, no reviewer attribution, and no version tags.
- Developers express frustration that compliance is a "moving target" and begin treating governance requirements as suggestions rather than obligations.

**Consequences:**
- Compliance becomes practically impossible because teams cannot track what the current rules are—let alone implement them.
- Trust in governance erodes, and teams disengage from the process entirely, defaulting to their own judgment.
- The review board spends increasing time adjudicating disputes about which version of a policy applies to a given decision.

**Correct approach:** [Version-control governance artifacts like source code](best-practices.md#practice-7-version-control-governance-artifacts-like-source-code). Establish a release cadence for policy changes, provide advance notice of upcoming changes, and honor transition periods during which both the old and new versions are valid. Apply the principle of [Governance Is a Living Process](principles.md#4-governance-is-a-living-process)—living means evolving deliberately, not changing chaotically.

---

## 5. The Classification Vacuum

**Description:** The organization has no data classification scheme—or has one that does not extend to generative-AI use cases. Teams sending data to model endpoints have no way to determine whether the data is appropriate for the model's deployment pattern. Classification is either absent, optional, or so complex that teams skip it.

**Symptoms:**
- When asked what classification tier applies to their data, teams respond with uncertainty or say "we don't classify data for AI."
- The gateway layer has no classification-based routing rules because there is no classification to route on.
- Risk assessments for new use cases cannot be completed because the data-sensitivity input is unknown.

**Consequences:**
- The principle of [Classification Drives Control](principles.md#2-classification-drives-control) is inoperative: without classification, all data is treated the same, which means either everything is over-restricted or everything is under-protected.
- Sensitive data flows to inappropriate model endpoints because there is no mechanism to distinguish it from non-sensitive data.
- Regulatory compliance cannot be demonstrated because the organization cannot show that data-handling rules are applied based on sensitivity.

**Correct approach:** [Use data classification tiers that match your existing data governance](best-practices.md#practice-4-use-data-classification-tiers-that-match-your-existing-data-governance). If no enterprise classification exists, treat generative-AI adoption as the catalyst to create one. Start with two tiers (sensitive and non-sensitive) and refine from there.

---

## 6. The Absentee Owner

**Description:** Use cases are approved and deployed but have no accountable individual responsible for their ongoing operation, compliance, and outcomes. Ownership is assigned to a team name, a shared alias, or simply left blank. When the original project lead moves on, no ownership transfer occurs.

**Symptoms:**
- The use case registry contains entries with team names or generic roles in the "owner" field rather than specific individuals.
- When an issue arises with a deployed use case, there is no clear person to contact; inquiries bounce between teams.
- Use cases continue running in production months or years after the business need has changed, consuming resources without delivering value.

**Consequences:**
- Incident response is delayed because no one is empowered or accountable to act on behalf of the use case.
- Cost accumulates on orphaned use cases that no one is actively managing, monitoring, or optimizing.
- Compliance drift occurs silently: the use case was compliant when launched, but changes in policy, data sources, or model behavior have introduced gaps that no one is tracking.

**Correct approach:** Apply the principle of [Every Use Case Has an Owner](principles.md#5-every-use-case-has-an-owner). Require a named individual in the use case registry, enforce ownership transfer as a governance event, and mandate periodic owner attestation. Use cases without a valid owner are flagged for review and, if unresolved, decommissioned.
