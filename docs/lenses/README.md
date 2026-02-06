# Lenses

## What Lenses Are

A lens is an industry-specific or domain-specific extension that overlays additional requirements onto the base GenAI Adoption Framework. The base framework defines universal capabilities, principles, and controls that apply to any organization adopting generative AI. Lenses acknowledge that universality has limits: a healthcare organization faces regulatory obligations that a media company does not; a financial institution operates under model risk management requirements that have no equivalent in retail; a government agency must satisfy authorization processes that the private sector does not encounter.

Lenses address this gap without fragmenting the framework. Rather than maintaining separate versions of the framework for each industry, lenses add domain-specific requirements, considerations, and constraints on top of the shared foundation. The base framework remains the single source of truth for architecture and principles. Lenses extend it for context.

## How Lenses Work

A lens does not replace or restructure the base framework. It adds to it. Specifically, a lens may:

- **Add requirements to existing layers.** A lens might add specific logging fields to the gateway layer, additional review criteria to the governance layer, or extra test cases to the workload layer. These additions sit alongside the base requirements, not in place of them.
- **Add checklist items.** A lens may define supplementary checklist items that must be satisfied in addition to the base checklists documented in this framework.
- **Add cross-cutting concerns.** A lens may introduce domain-specific risks or compliance obligations that span multiple layers and must be tracked across the full stack.
- **Tighten existing controls.** A lens may specify that a control which is optional in the base framework becomes mandatory in the lens context, or that a threshold which is recommended becomes a hard requirement.

A lens never relaxes a base framework requirement. The base controls are the floor. Lenses only raise it.

Each lens follows a consistent structure: an overview of the regulatory or domain context, a layer-by-layer enumeration of additional requirements, supplementary checklist items, and references to the specific regulations or standards that motivate the additions.

## Planned Lenses

The following lenses are indexed for future development. They are not yet authored. This list represents the domains where the base framework most clearly requires supplementary guidance, based on regulatory complexity and the pace of generative AI adoption within each sector.

**Healthcare Lens** --- Addresses the requirements of organizations that handle protected health information (PHI) and operate under healthcare data privacy regulations. Key areas include: mapping framework layers to healthcare privacy rule requirements, defining PHI-specific classification tiers and handling rules, establishing guardrails for clinical decision support use cases, and specifying audit trail requirements for healthcare compliance.

**Financial Services Lens** --- Addresses the requirements of banking, insurance, and capital markets organizations operating under financial regulation. Key areas include: mapping framework layers to financial audit and payment card security requirements, integrating model risk management practices (consistent with supervisory guidance on model risk), addressing fair lending and bias considerations in generative AI outputs, and specifying controls for customer-facing financial content generation.

**Government Lens** --- Addresses the requirements of public sector organizations operating under government security and authorization frameworks. Key areas include: mapping framework layers to federal security control baselines, aligning gateway and operations controls with government cloud authorization requirements, defining authority-to-operate (ATO) processes for generative AI systems, and specifying data sovereignty and classification controls for government data.

**Regulated Industries Lens** --- Captures common patterns that recur across multiple regulated verticals. Rather than duplicating guidance, this lens extracts shared regulatory themes --- audit trail integrity, data residency, model explainability, human oversight for high-stakes decisions --- and provides a consolidated reference that organizations in any regulated sector can use as a starting point before applying a more specific lens.

## Contributing a Lens

Organizations with domain expertise are encouraged to contribute lenses. A contributed lens should:

1. Follow the structure described above: regulatory context, layer-by-layer additions, supplementary checklist items, and regulatory references.
2. Add to the base framework without modifying it. If a lens reveals a gap in the base framework, that gap should be raised as a separate contribution to the base.
3. Maintain the framework's vendor-agnostic constraint. Lenses must not reference specific products, services, or vendor platforms by name.
4. Include citations to the specific regulations, standards, or supervisory guidance that motivate each addition.

See the [Contributing Guide](../../CONTRIBUTING.md) for the pull request process, style guidelines, and review requirements.

---

*Navigate back to the [Framework Overview](../overview.md) for the base architecture, or to the [Checklists](../checklists/) for the base verification criteria that lenses extend.*
