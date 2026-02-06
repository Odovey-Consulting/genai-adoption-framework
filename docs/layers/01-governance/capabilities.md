# Governance Capabilities

> **Layer:** 01 — Governance
> **Purpose:** Enumerate the capabilities an organization must build or acquire to operate a governed generative-AI practice. Each capability is mapped to the maturity stage at which it becomes critical.

Capabilities are grouped by functional area. For each capability, the entry specifies what it is, why it is required, and the maturity stage at which the organization cannot proceed without it. Maturity stages referenced throughout are: **Crawl** (initial pilots), **Walk** (scaling across teams), **Run** (enterprise-wide, production-grade operations).

---

## Functional Area: Policy Management

### 1. Acceptable Use Policy (AUP) Definition and Distribution

**Description:** A documented policy that defines permitted and prohibited uses of generative-AI capabilities across the organization. The AUP covers topics including approved use-case categories, data-handling requirements, output-review obligations, prohibited prompt patterns, and consequences for violations.

**Why it is required:** Without a clearly articulated AUP, individual teams define their own rules—or operate with none. The AUP serves as the single authoritative source that legal, compliance, security, and engineering teams reference when evaluating whether a use case is permissible. It also provides the contractual basis for enforcement.

**Critical maturity stage:** **Crawl.** The AUP must exist before the first pilot enters development. Even a minimal, one-page AUP is better than none. The policy grows in specificity as the organization moves through Walk and Run.

**Key outputs:** Published AUP document, acknowledgment tracking per employee or team, distribution mechanism (intranet, onboarding flow, or integrated developer portal).

---

### 2. Exemption Request and Approval Process

**Description:** A formal workflow for teams to request exceptions to existing governance policies when a legitimate use case falls outside current rules. The process includes a submission template, risk-assessment criteria, approval authority designation, time-bound exemption windows, and mandatory re-review dates.

**Why it is required:** Rigid policies without an escape valve drive teams to work around governance entirely. An exemption process channels edge cases through a structured review, ensuring risks are evaluated while preserving the integrity of the broader policy framework. Exemption data also signals where policies need updating.

**Critical maturity stage:** **Walk.** During Crawl, the limited number of use cases can be handled directly by the review board. As adoption scales in Walk, the volume and diversity of use cases demand a self-service exemption pathway.

**Key outputs:** Exemption request form, decision log with rationale, time-bound approval records, metrics on exemption volume and disposition.

---

## Functional Area: Data Governance

### 3. Data Classification Scheme

**Description:** A tiered classification system that labels data assets by sensitivity level, with explicit handling rules for each tier. The scheme defines classification criteria, labeling standards, storage and transmission requirements, and which generative-AI deployment patterns are permissible for each tier.

**Why it is required:** Classification is the mechanism that translates abstract risk tolerance into concrete, enforceable controls. It enables the gateway layer to route requests appropriately, allows security teams to set differential monitoring thresholds, and gives development teams clear guidance on what data they can use in prompts, fine-tuning, or retrieval-augmented generation pipelines.

**Critical maturity stage:** **Crawl.** Even a two-tier scheme (sensitive / non-sensitive) is required before the first model endpoint is provisioned. The scheme should be refined to three or four tiers during Walk. See the [Decision Guide](decision-guide.md) for tier-count considerations.

**Key outputs:** Classification tier definitions, labeling standards, handling-rule matrix (tier vs. permitted model deployment pattern), integration points with existing enterprise data-governance tooling.

---

## Functional Area: Model Governance

### 4. Model Catalog and Approval Process

**Description:** A centralized registry of all generative-AI models approved for use within the organization. Each catalog entry includes the model's deployment pattern (self-hosted, managed service, external API), approved data-classification tiers, cost profile, known limitations, and the conditions under which it may be used.

**Why it is required:** Without a catalog, teams independently evaluate and adopt models, leading to redundant spending, inconsistent security postures, and ungoverned data flows. The catalog provides a curated "menu" that balances choice with control. The approval process ensures new models are vetted against security, privacy, cost, and quality criteria before entering the catalog.

**Critical maturity stage:** **Crawl.** The initial catalog may contain only one or two approved models. The approval process formalizes during Walk to handle the growing volume of model evaluation requests.

**Key outputs:** Model registry (name, version, deployment pattern, approved tiers, cost baseline, owner), approval workflow definition, evaluation criteria checklist, retirement and deprecation procedures.

---

### 5. Use Case Review and Approval Workflow

**Description:** A structured process for teams to propose, document, and obtain approval for new generative-AI use cases. The workflow captures the business objective, data requirements, model selection, risk assessment, human-oversight design, success metrics, and cost estimate for each proposed use case.

**Why it is required:** Use case review is the primary mechanism for applying governance principles to real work. It ensures that every deployment is evaluated against the AUP, classification scheme, and human-oversight requirements before resources are committed. It also creates the registry that enables portfolio-level visibility into how generative AI is being used across the organization.

**Critical maturity stage:** **Crawl.** The review process may be informal (a single meeting with the review board) during Crawl. It must formalize into a repeatable workflow with templates and SLAs during Walk.

**Key outputs:** Use case proposal template, review checklist, approval/rejection decision log, use case registry with owner and status tracking.

---

## Functional Area: Financial Governance

### 6. Cost Governance Framework

**Description:** A set of controls for managing generative-AI spending, including per-team and per-use-case budgets, chargeback or showback mechanisms, consumption forecasting, and threshold-based alerts or hard caps.

**Why it is required:** Generative-AI cost structures—typically usage-based and driven by token volume—are unfamiliar to most enterprise budgeting processes. Without cost governance, a single poorly optimized pipeline can consume a quarter's budget in days. The framework provides visibility and control before spending becomes a crisis.

**Critical maturity stage:** **Walk.** During Crawl, spending is typically small enough to manage informally. As teams scale in Walk, consumption-based costs grow non-linearly, and the organization needs formal budgets, alerts, and accountability. See the [Decision Guide](decision-guide.md) for cap-style decisions.

**Key outputs:** Budget allocation model, chargeback/showback reporting, consumption dashboards, alert thresholds, escalation procedures for budget overruns.

---

## Functional Area: Oversight and Review

### 7. Review Board Cadence and Decision-Making Process

**Description:** A cross-functional governance body that meets on a defined cadence to review use case proposals, policy updates, exemption requests, incident reports, and cost trends. The board has a documented charter that specifies membership, quorum requirements, decision authority, escalation paths, and communication protocols.

**Why it is required:** Governance decisions require input from multiple disciplines—legal, security, engineering, product, finance, and business leadership. Without a formal board, decisions are made in silos or not at all. The fixed cadence ensures governance does not become a bottleneck while still maintaining rigor.

**Critical maturity stage:** **Crawl.** The board may meet ad hoc during very early exploration, but a fixed cadence (at least monthly) must be established before the first use case enters production. During Run, the board shifts toward exception handling and strategic direction as routine approvals become streamlined.

**Key outputs:** Board charter, membership roster, meeting cadence, decision log, published meeting minutes or summaries, escalation matrix.

---

### 8. Incident Reporting and Escalation Procedures

**Description:** A defined process for identifying, reporting, triaging, and escalating generative-AI-related incidents. Covered incident types include data leakage through prompts, harmful or biased model outputs, unexpected cost spikes, policy violations, and service disruptions affecting downstream systems.

**Why it is required:** Generative-AI systems introduce novel failure modes—hallucinations, prompt injection, unintended data exposure—that existing incident-response playbooks may not cover. A dedicated reporting and escalation procedure ensures these incidents are captured, categorized, and resolved with the urgency and expertise they require.

**Critical maturity stage:** **Walk.** During Crawl, incidents can be handled through existing channels with ad-hoc escalation. As production use cases multiply in Walk, dedicated procedures become essential to prevent incidents from falling through the cracks of general IT incident management.

**Key outputs:** Incident taxonomy (generative-AI-specific categories), reporting channel and template, severity classification matrix, escalation paths with response-time SLAs, post-incident review process.

---

## Functional Area: Compliance

### 9. Regulatory Mapping and Compliance Tracking

**Description:** A continuously maintained mapping between the organization's generative-AI activities and the regulatory requirements that apply to them. This includes jurisdiction-specific AI regulations, industry-specific rules (financial services, healthcare, etc.), data-protection laws, and sector-specific guidance from regulatory bodies.

**Why it is required:** The regulatory landscape for AI is evolving rapidly across multiple jurisdictions. Organizations that do not actively track applicable regulations risk non-compliance, fines, and reputational damage. Regulatory mapping also informs the AUP, classification scheme, and use case review criteria, ensuring they remain aligned with external requirements.

**Critical maturity stage:** **Walk.** During Crawl, a basic regulatory scan is sufficient. As the organization scales in Walk and use cases touch more jurisdictions and data types, a structured compliance-tracking capability becomes essential. During Run, this capability should integrate with enterprise-wide compliance and risk-management functions.

**Key outputs:** Regulatory requirements inventory, mapping of requirements to governance controls, gap analysis, compliance attestation schedule, regulatory change monitoring process.
