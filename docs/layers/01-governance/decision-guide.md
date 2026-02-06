# Governance Decision Guide

> **Layer:** 01 — Governance
> **Purpose:** Provide structured analysis for the major governance decisions organizations must make when adopting generative AI. Each decision includes context, options, evaluation criteria, and tradeoffs.

These decisions are not one-time choices. Each should be revisited as the organization matures through Crawl, Walk, and Run stages. The principle of [Governance Is a Living Process](principles.md#4-governance-is-a-living-process) applies directly here.

---

## Decision 1: How Many Data Classification Tiers?

**Context:** The data classification scheme determines how granularly the organization can differentiate controls. Too few tiers and sensitive data may be under-protected or low-risk data over-restricted. Too many tiers and the classification process becomes a bottleneck that teams avoid.

### Options

| Option | Description |
|--------|-------------|
| **Two tiers** | Sensitive and non-sensitive. Binary classification. |
| **Four tiers** | Typically: public, internal, confidential, restricted. Graduated controls. |
| **Match existing scheme** | Adopt whatever classification the enterprise already uses, adding AI-specific handling rules. |

### Evaluation Criteria

| Criterion | Two Tiers | Four Tiers | Match Existing |
|-----------|-----------|------------|----------------|
| Speed to implement | Fast — minimal decisions | Moderate — requires definitions | Depends on existing scheme maturity |
| Precision of control | Low — blunt instrument | High — proportionate controls | Matches current enterprise precision |
| Team adoption friction | Low — easy to understand | Moderate — requires training | Low — teams already know the labels |
| Scalability to Run stage | Poor — will need expansion | Good — covers most scenarios | Good — integrated with enterprise tooling |
| Alignment with compliance | Weak — regulators expect granularity | Strong — maps well to regulatory tiers | Strong — already vetted by compliance |

### Tradeoffs

A two-tier scheme is the fastest to deploy and easiest to understand, making it suitable for organizations in the Crawl stage with limited use cases. However, it forces a binary decision that either over-restricts internal data (treating it the same as restricted data) or under-protects confidential data (treating it the same as public data). Most organizations will need to expand beyond two tiers before entering the Walk stage.

A four-tier scheme provides the granularity needed for proportionate controls but requires more upfront effort to define tier boundaries, train teams, and configure gateway routing rules. If the organization does not have an existing classification culture, introducing four tiers simultaneously with generative-AI adoption creates a compound change-management challenge.

Matching the existing enterprise scheme is the recommended default when a mature scheme exists. It eliminates redundancy, leverages existing training and tooling, and aligns AI governance with broader data governance. The only risk is that the existing scheme may lack tiers relevant to AI-specific concerns (e.g., distinguishing between data used in prompts versus data used for fine-tuning), which can be addressed by adding handling-rule subtypes within existing tiers rather than creating new tiers.

**Recommendation:** Match the existing enterprise scheme if one exists with three or more tiers. If no scheme exists or the current scheme has only two tiers, implement a four-tier scheme and use generative-AI adoption as the catalyst for broader data governance maturity.

---

## Decision 2: Centralized vs. Federated Model Approval

**Context:** Someone must decide which generative-AI models are approved for use. This decision determines whether that authority sits with a central governance body or is distributed to individual business units or product teams within guardrails.

### Options

| Option | Description |
|--------|-------------|
| **Centralized** | A single governance body evaluates and approves all models for the entire organization. |
| **Federated** | Individual teams or divisions may approve models within their scope, subject to organization-wide criteria. |
| **Hybrid** | Central body maintains a pre-approved catalog; teams may add models within defined parameters without central approval. |

### Evaluation Criteria

| Criterion | Centralized | Federated | Hybrid |
|-----------|-------------|-----------|--------|
| Consistency of standards | High — uniform evaluation | Low — varies by team | Moderate — baseline consistent, extensions vary |
| Speed of approval | Slow — single bottleneck | Fast — distributed decision-making | Moderate — pre-approved is instant, additions take time |
| Governance overhead | High on central team | Low centrally, high in aggregate | Balanced — central team manages catalog, teams manage extensions |
| Risk of ungoverned models | Low | High — standards may diverge | Moderate — bounded by parameters |
| Suitability for Crawl | Good — few models, low volume | Poor — overhead without benefit | Poor — unnecessary complexity |
| Suitability for Run | Poor — bottleneck at scale | Moderate — if criteria are rigorous | Good — scales with guardrails |

### Tradeoffs

Centralized approval provides the strongest consistency and simplest audit trail. It works well during Crawl and early Walk when the model catalog is small and the approval volume is manageable. It becomes a bottleneck during Run when dozens of teams have diverse model requirements and the central body cannot evaluate requests quickly enough.

Federated approval maximizes team autonomy and speed but risks inconsistent evaluation standards, duplicated effort, and models entering production without adequate security or compliance review. It requires mature teams with strong governance discipline—a condition rarely met at Crawl or early Walk.

The hybrid model balances consistency with agility. The central body curates a pre-approved catalog that covers the majority of use cases; teams can select from this catalog without additional approval. For models outside the catalog, teams submit an evaluation request that the central body processes against published criteria. This model scales better than pure centralization while maintaining stronger guardrails than pure federation.

**Recommendation:** Start centralized during Crawl. Transition to hybrid during Walk as the catalog stabilizes and team maturity increases.

---

## Decision 3: Cost Governance — Hard Budget Caps vs. Alerts With Human Approval

**Context:** Generative-AI consumption costs are usage-based and can spike unpredictably. Organizations must decide whether to enforce hard spending limits that automatically cut off access when reached, or to use alert-based systems that notify a human who then decides whether to approve continued spending.

### Options

| Option | Description |
|--------|-------------|
| **Hard caps** | Access is automatically suspended when the budget threshold is reached. Service resumes only after explicit reauthorization. |
| **Alerts with human approval** | Notifications are sent at defined thresholds. A designated approver must authorize continued spending, but service is not interrupted. |
| **Tiered approach** | Alerts at lower thresholds, hard caps at an upper ceiling. Provides warning before cutoff. |

### Evaluation Criteria

| Criterion | Hard Caps | Alerts + Approval | Tiered |
|-----------|-----------|-------------------|--------|
| Cost certainty | High — spending cannot exceed cap | Low — depends on human response time | Moderate — bounded by ceiling |
| Service continuity risk | High — production workloads may be interrupted | Low — service continues while approval is pending | Moderate — only interrupted at ceiling |
| Operational overhead | Low — automated enforcement | High — requires responsive approvers | Moderate — most handled by alerts |
| Suitability for production | Risky — customer-facing services may drop | Good — no interruption | Good — ceiling prevents runaway costs without routine disruption |

### Tradeoffs

Hard caps provide absolute cost certainty but introduce the risk of production service interruption. If a customer-facing generative-AI feature hits its budget cap mid-day, the feature goes offline until a human intervenes. This tradeoff is acceptable for development and staging environments but rarely acceptable for production workloads.

Alerts with human approval preserve service continuity but depend entirely on the responsiveness and judgment of the designated approver. If the approver is unavailable or if alerts are too frequent (leading to fatigue), costs can exceed expectations significantly before action is taken.

The tiered approach is the most operationally sound: alerts at lower thresholds prompt proactive cost management (prompt optimization, caching, model downsizing), while a hard cap at a significantly higher ceiling provides a safety net against runaway spending. The gap between the alert threshold and the hard cap should be large enough to allow time for human response but small enough to limit financial exposure.

**Recommendation:** Use the tiered approach. Set alerts at 60% and 85% of budget. Set hard caps at 120% of budget for non-production environments and 150% for production environments (with automatic escalation to the use case owner and finance at 100%).

---

## Decision 4: AUP Scope — Organization-Wide vs. Division-Specific

**Context:** Large organizations must decide whether one acceptable use policy covers all divisions or whether each division maintains its own AUP tailored to its regulatory environment, risk profile, and use-case portfolio.

### Options

| Option | Description |
|--------|-------------|
| **Organization-wide** | A single AUP applies to all divisions. |
| **Division-specific** | Each division writes and maintains its own AUP. |
| **Layered** | An organization-wide base AUP sets minimum standards; divisions may add (but not relax) requirements. |

### Evaluation Criteria

| Criterion | Organization-Wide | Division-Specific | Layered |
|-----------|-------------------|-------------------|---------|
| Consistency | High | Low | Moderate — baseline consistent |
| Regulatory fit | Weak for multi-industry conglomerates | Strong — tailored to each context | Strong — divisions address specifics |
| Maintenance cost | Low — one document | High — many documents, potential drift | Moderate — base plus increments |
| Clarity for shared services | High — one set of rules | Low — which AUP applies to shared platforms? | High — shared services follow the base |

### Tradeoffs

An organization-wide AUP is simplest to maintain and communicate. It works well when all divisions operate under similar regulatory regimes and risk profiles. It struggles in conglomerates where one division handles regulated health data and another handles only public marketing content—a single policy must either be restrictive enough for the most sensitive division (frustrating the others) or permissive enough for the least sensitive (under-protecting the others).

Division-specific AUPs maximize regulatory fit but create fragmentation: shared infrastructure teams must navigate multiple policy sets, cross-division collaboration becomes complicated, and the governance board must review and harmonize multiple documents.

The layered approach provides the best balance for most organizations. The base AUP establishes universal principles and minimum standards (aligning with the [governance principles](principles.md)). Divisions add supplementary rules that address their specific regulatory, contractual, or risk requirements. Critically, divisions may add restrictions but may not relax the base standards, preserving a consistent floor of governance.

**Recommendation:** Use the layered approach. Start with an organization-wide base AUP during Crawl. Allow divisions to add supplementary rules during Walk as adoption diversifies across business contexts.

---

## Decision 5: Review Cadence — Monthly vs. Quarterly vs. Event-Triggered

**Context:** The governance review board must meet regularly, but the cadence affects both governance responsiveness and operational overhead. Too frequent and the board becomes a burden; too infrequent and decisions queue for too long.

### Options

| Option | Description |
|--------|-------------|
| **Monthly** | Fixed monthly meetings covering all governance business. |
| **Quarterly** | Fixed quarterly meetings for strategic reviews and policy updates. |
| **Event-triggered** | Meetings convened only when a specific event requires review (new use case, incident, regulatory change). |
| **Quarterly + event-triggered** | Scheduled quarterly reviews supplemented by event-triggered sessions as needed. |

### Evaluation Criteria

| Criterion | Monthly | Quarterly | Event-Triggered | Quarterly + Triggered |
|-----------|---------|-----------|-----------------|----------------------|
| Responsiveness | High | Low — up to 3-month wait | High — immediate | High — no longer than 3 months between reviews |
| Board member burden | High — 12 meetings/year | Low — 4 meetings/year | Variable — unpredictable | Moderate — 4 scheduled + ad hoc |
| Suitability for Crawl | Good — frequent check-ins | Too slow for early learning | Good — but may be too reactive | Good |
| Suitability for Run | Excessive if most items are routine | Too slow for high-volume programs | Chaotic at scale | Good — routine in quarterly, exceptions as needed |

### Tradeoffs

Monthly cadence provides high responsiveness and works well during Crawl and early Walk when decisions are frequent and the governance process is still being refined. As the program matures, monthly meetings may lack sufficient agenda items, leading to performative meetings that waste board members' time.

Quarterly cadence is appropriate for mature programs where routine approvals are delegated and the board focuses on strategic direction, policy updates, and exception review. It is too slow during Crawl when the volume of first-time decisions is high and teams cannot wait three months for approval.

Event-triggered cadence is maximally responsive but creates unpredictable scheduling, makes it difficult to maintain board engagement, and risks important-but-not-urgent items (like policy reviews) being perpetually deferred because no event triggers them.

The combined quarterly-plus-event-triggered model offers the best balance: strategic reviews and policy updates happen on a predictable quarterly schedule, while urgent items (incidents, time-sensitive use cases, regulatory changes) trigger ad-hoc sessions with a defined convening protocol.

**Recommendation:** Start monthly during Crawl. Transition to quarterly-plus-event-triggered during Walk once the review board has established its processes and routine approvals are delegated to standing criteria.
