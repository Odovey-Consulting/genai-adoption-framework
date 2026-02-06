# Governance Best Practices

> **Layer:** 01 — Governance
> **Purpose:** Actionable practices that help organizations implement governance capabilities effectively. Each practice includes guidance, rationale, and the expected outcome when applied consistently.

Practices are organized by concern area. Where a practice directly counters a known failure mode, the corresponding [anti-pattern](anti-patterns.md) is cross-referenced.

---

## Concern Area: Acceptable Use Policy

### Practice 1: Write the AUP in Plain Language Developers Actually Read

**Guidance:** Draft the acceptable use policy in clear, direct prose at a reading level any employee can understand. Avoid legal jargon unless a specific clause requires precise legal phrasing—and when it does, add a plain-language summary alongside it. Structure the AUP with scannable headings, numbered rules, and concrete examples of permitted and prohibited behavior. Keep the core document under five pages; relegate detailed technical specifications to linked appendices.

**Rationale:** A policy that nobody reads provides no governance value. Developers and business users routinely skip documents written in dense legalese, which means the policy exists on paper but not in practice. Plain language increases comprehension, reduces support queries, and makes acknowledgment meaningful rather than performative. When people understand the rules, voluntary compliance rises and enforcement burden drops.

**Expected outcome:** Teams can articulate the key AUP rules without referring to the document. Policy violations attributed to ignorance decrease measurably. New-hire onboarding includes AUP review as a five-minute exercise, not a legal briefing.

**Counters anti-pattern:** [The Paper Tiger](anti-patterns.md#3-the-paper-tiger) — policies that exist but nothing enforces them often begin as policies nobody understands.

---

### Practice 2: Map Every AUP Rule to a Gateway Enforcement Mechanism

**Guidance:** For each rule in the AUP, document how it is enforced technically. If a rule states "no personally identifiable information in prompts," specify the detection mechanism in the gateway layer that scans for PII before the request reaches the model. If a rule cannot be enforced technically today, document it as a manual control and assign an owner responsible for monitoring compliance until automation is available. Maintain a traceability matrix: AUP rule number, enforcement mechanism, enforcement layer (gateway, orchestration, application), and current status (automated, manual, planned).

**Rationale:** Policies without enforcement are suggestions. In fast-moving development environments, suggestions are ignored under deadline pressure. Technical enforcement removes the burden from individual judgment and makes compliance the default path. The traceability matrix also reveals governance gaps early, before they become incidents.

**Expected outcome:** Every AUP rule has a documented enforcement mechanism or an explicit risk-acceptance decision for rules that remain manually controlled. The traceability matrix is reviewed at each governance cycle and drives the automation backlog.

**Counters anti-pattern:** [The Paper Tiger](anti-patterns.md#3-the-paper-tiger) — enforcement transforms paper policies into operational controls.

---

## Concern Area: Model Management

### Practice 3: Start With a Conservative Model Catalog and Expand Deliberately

**Guidance:** Launch the model catalog with the smallest number of models that satisfy the initial approved use cases—often one or two. Define clear criteria for adding new models: security review completed, data-handling terms reviewed by legal, cost profile documented, performance benchmarks established against the relevant use-case requirements. New models enter the catalog only through the formal approval process, never through ad-hoc adoption.

**Rationale:** Every model in the catalog is a surface the organization must govern: monitor for cost, audit for compliance, track for version changes, and support operationally. Starting conservatively keeps the governance burden manageable during early maturity stages. Expanding deliberately ensures each addition is justified by a use-case need rather than curiosity or vendor promotion. This approach also simplifies the gateway configuration and reduces the testing matrix.

**Expected outcome:** The model catalog grows in proportion to validated demand. Teams are not blocked—they have a clear path to request new models—but the catalog does not accumulate unused or redundant entries. Governance overhead scales linearly with actual adoption, not with the total number of available models in the market.

**Counters anti-pattern:** [The Wild West](anti-patterns.md#1-the-wild-west) — a curated catalog prevents ungoverned model proliferation.

---

## Concern Area: Data Governance

### Practice 4: Use Data Classification Tiers That Match Your Existing Data Governance

**Guidance:** Do not create a new classification scheme exclusively for generative AI. Instead, extend the organization's existing data-classification framework with generative-AI-specific handling rules. If the organization classifies data into four tiers (e.g., public, internal, confidential, restricted), define for each tier which model deployment patterns are permitted, what prompt-sanitization controls are required, and whether outputs require human review. If no enterprise classification scheme exists, use this as the catalyst to create one—but design it for the whole organization, not just for AI workloads.

**Rationale:** Parallel classification schemes create confusion. Teams already trained on existing data labels should not have to learn a second system for AI use cases. Alignment also ensures that existing data-governance tooling—catalogs, access controls, audit trails—can be leveraged rather than rebuilt. A unified scheme reduces the risk that data classified as "restricted" in one system is treated as "internal" in the AI context due to mapping errors.

**Expected outcome:** A single classification scheme governs data across all contexts, including generative-AI workloads. Teams apply labels they already understand. Existing access-control and monitoring infrastructure extends to cover AI data flows without requiring parallel tooling.

**Counters anti-pattern:** [The Classification Vacuum](anti-patterns.md#5-the-classification-vacuum) — extending an existing scheme is the fastest path to closing the classification gap.

---

## Concern Area: Financial Governance

### Practice 5: Establish Cost Ceilings Before Teams Start Building

**Guidance:** Set explicit per-team and per-use-case spending limits before generative-AI development begins. Define three thresholds for each budget: an informational alert (e.g., 60% of budget consumed), a warning alert requiring justification to continue (e.g., 85%), and a hard or soft cap at 100% that triggers a defined escalation process. Publish the cost model—including token pricing, infrastructure costs, and any shared-service charges—so teams can estimate costs during the design phase rather than discovering them in production.

**Rationale:** Usage-based pricing models make generative-AI costs highly variable and difficult to predict without constraints. Teams that build without cost awareness routinely exceed expectations by an order of magnitude once real user traffic arrives. Establishing ceilings upfront forces cost-conscious design decisions (prompt optimization, caching strategies, model-size selection) into the architecture phase, where they are cheapest to implement.

**Expected outcome:** No team exceeds its budget without a deliberate, documented decision to do so. Cost surprises are rare because the alert system provides early warning. Architecture decisions reflect cost trade-offs from the outset, not as an afterthought.

---

## Concern Area: Oversight Structure

### Practice 6: Build the Review Board With Cross-Functional Representation

**Guidance:** Compose the governance review board to include, at minimum, representatives from: engineering or architecture, information security, legal or compliance, finance, and at least one business-domain leader from a team actively building generative-AI use cases. Rotate the business-domain seat periodically to ensure diverse perspectives. Define a quorum requirement and a decision-making protocol (consensus, majority, or designated authority with advisory input). Publish the board's charter, membership, and decision log transparently.

**Rationale:** Generative-AI governance decisions sit at the intersection of technology, law, security, economics, and business strategy. A board composed solely of technologists will underweight regulatory risk; a board of only legal and compliance staff will overweight restriction. Cross-functional composition ensures decisions are balanced, practical, and credible to the teams that must follow them. Transparency in the decision log builds trust and reduces the perception that governance is arbitrary.

**Expected outcome:** Governance decisions reflect multiple perspectives and are accepted by stakeholders across the organization. The review board is viewed as an enabler rather than a gatekeeper. Decision rationale is documented and referenceable, reducing re-litigation of settled questions.

**Counters anti-pattern:** [The Ivory Tower](anti-patterns.md#2-the-ivory-tower) — cross-functional representation prevents governance from becoming disconnected from operational reality.

---

## Concern Area: Governance Operations

### Practice 7: Version-Control Governance Artifacts Like Source Code

**Guidance:** Store all governance documents—AUP, classification scheme, model catalog, review board charter, cost thresholds, exemption records—in a version-controlled repository. Use the same branching, review, and merge practices the organization applies to source code. Every change requires a pull request with at least one reviewer from the governance board. Tag releases with semantic versioning and maintain a changelog that summarizes what changed and why.

**Rationale:** Governance artifacts that live in shared documents or wikis suffer from untracked edits, unclear authorship, and no reliable rollback mechanism. Version control provides a complete audit trail, enforces review discipline, and makes it trivial to compare the current policy against any prior version. It also signals to engineering teams that governance is maintained with the same rigor as production code, increasing credibility.

**Expected outcome:** Every governance change is traceable to a specific author, reviewer, and rationale. Auditors can reconstruct the governance posture at any point in time. Teams trust that the latest version in the repository is the authoritative source—eliminating the "which version is current?" problem.

**Counters anti-pattern:** [The Moving Target](anti-patterns.md#4-the-moving-target) — version control with release discipline prevents governance from changing without notice.
