# Cross-Cutting Concerns

## Why Cross-Cutting Concerns Exist

The GenAI Adoption Framework organizes capabilities into five stacking layers: Governance, Gateway, Operations, Workloads, and Agent Security. Each layer has a clear owner, a bounded scope, and a defined interface with the layers above and below it. This structure works well for capabilities that live cleanly within a single layer.

Some concerns refuse to stay in their lane. Risk management is not a governance problem alone. Compliance obligations do not stop at the gateway. Responsible AI principles cannot be enforced by a single team or a single control. These concerns cut across every layer, every maturity stage, and every team that touches the framework.

Cross-cutting concerns are separated from the layer documentation for three reasons:

1. **They span all five layers.** Attempting to document them within a single layer creates blind spots. A risk that originates in Layer 4 (Workloads) may only be detectable through Layer 3 (Operations) and only governable through Layer 1 (Governance). Placing the full risk taxonomy in any one layer misrepresents where responsibility lives.

2. **They span all three maturity stages.** An organization at the crawl stage still carries compliance obligations. A team at the run stage still faces tradeoffs between security and velocity. Maturity changes the sophistication of the response, not whether the concern applies.

3. **They require coordination across team boundaries.** Platform teams, workload teams, security teams, and executive sponsors all hold a piece of each cross-cutting concern. No single owner can resolve them unilaterally.

## The Five Cross-Cutting Concerns

### Risk Management

A taxonomy of risks specific to generative AI systems, covering model behavior, data exposure, security threats, operational fragility, and autonomous agent failures. Includes a risk assessment matrix and a mapping of which framework layers mitigate which risks.

### Compliance and Regulatory Mapping

A crosswalk between the framework's layers and major regulatory frameworks and standards. Maps framework controls to external obligations so that compliance teams can trace audit evidence back to specific layer capabilities.

### Responsible AI

Concrete principles (fairness, transparency, explainability, human oversight, accountability, privacy) translated into enforceable controls at each layer. Moves beyond aspirational statements into specific mechanisms that each layer must implement.

### Organizational Readiness

Team structures, skills assessments, training paths, change management strategies, and executive sponsorship requirements. Defines who owns what, what skills they need, and how team composition evolves as maturity increases.

### Tradeoffs

An honest treatment of the tensions that arise when principles conflict. Security versus velocity. Centralization versus autonomy. Cost versus capability. Governance maturity versus speed to market. Comprehensive observability versus privacy. Each tradeoff includes guidance on when to favor each side and warning signs of over-indexing.

## How to Use This Section

Read these documents alongside the layer documentation, not after it. When implementing any layer, consult the cross-cutting concerns to understand obligations that the layer documentation alone does not capture. When assessing organizational maturity, use the organizational readiness guide to confirm that team structures and skills match the maturity stage you are targeting.

## Navigation

| Concern | Document | Primary Audience |
|---|---|---|
| Risk Management | [risk-management.md](risk-management.md) | Risk officers, platform leads, security teams |
| Compliance and Regulatory Mapping | [compliance.md](compliance.md) | Compliance teams, legal, auditors |
| Responsible AI | [responsible-ai.md](responsible-ai.md) | AI leads, ethics reviewers, product owners |
| Organizational Readiness | [organizational-readiness.md](organizational-readiness.md) | Engineering managers, executives, HR leads |
| Tradeoffs | [tradeoffs.md](tradeoffs.md) | Architects, engineering leads, decision-makers |
