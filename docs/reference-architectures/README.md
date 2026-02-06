# Reference Architectures

## Purpose

Reference architectures provide logical blueprints for deploying generative AI capabilities within an organization. They are not prescriptive infrastructure diagrams tied to any particular vendor or product. Instead, they describe **functional components**, **communication patterns**, and **trust boundaries** that remain constant regardless of which specific tools, platforms, or services fill each role.

Each architecture in this section represents a deployment topology that addresses a distinct set of organizational constraints. They are starting points, not finish lines. Every enterprise will need to adapt these patterns to its own regulatory landscape, existing infrastructure investments, team capabilities, and risk posture.

## How to Use These Architectures

1. **Identify your constraints first.** Before selecting an architecture, catalog the non-negotiable requirements: data residency mandates, network connectivity restrictions, existing identity systems, compliance frameworks, and budget boundaries.

2. **Select the closest match.** Choose the reference architecture whose assumptions most closely align with your constraints. No architecture will be a perfect fit out of the box.

3. **Adapt and extend.** Use the design decisions narrative in each architecture to understand *why* components are placed where they are. When you deviate from the reference, revisit those rationales to ensure your modifications preserve the security, observability, and governance properties you need.

4. **Layer in your specifics.** Replace each functional box label with the concrete service, platform, or tool your organization has standardized on. The architecture is deliberately product-agnostic so that this substitution step is straightforward.

5. **Validate with your teams.** Walk infrastructure, security, and application teams through the adapted architecture before implementation begins. Reference architectures succeed when they become shared mental models, not shelf documents.

## The Four Architectures

### Single-Cloud Architecture

All generative AI components are deployed within a single cloud provider's environment. This is the simplest topology, appropriate for organizations that have standardized on one cloud and have no regulatory requirement to operate across providers or on-premises. It minimizes network complexity and credential sprawl while relying on the provider's native capabilities for identity, networking, and observability.

**Best when:** You operate in one cloud, your data classification permits cloud processing, and you want the fastest path to production.

[Read the Single-Cloud Architecture](single-cloud.md)

### Multi-Cloud Architecture

A centralized gateway abstracts across model endpoints hosted by multiple cloud providers. This architecture suits organizations that need provider diversity for resilience, cost optimization, or access to specialized model capabilities that no single provider covers.

**Best when:** You consume models from multiple providers, need failover across clouds, or want to avoid single-provider lock-in at the model layer.

[Read the Multi-Cloud Architecture](multi-cloud.md)

### Hybrid Architecture

A combination of on-premises model infrastructure and cloud-hosted model endpoints, connected through a gateway that enforces routing policies based on data sensitivity, latency requirements, or cost targets. This architecture addresses data residency mandates while preserving access to cloud-scale model diversity.

**Best when:** Regulatory or policy constraints require certain data to remain on-premises, but you still need access to cloud-hosted models for less sensitive workloads.

[Read the Hybrid Architecture](hybrid.md)

### Air-Gapped Architecture

A fully self-contained deployment with no connectivity to external networks. Every component, from model weights to evaluation tooling to the gateway itself, operates within a closed perimeter. This architecture is designed for classified environments, high-security enclaves, and any context where network isolation is a hard requirement.

**Best when:** You operate in disconnected, classified, or otherwise network-restricted environments where no external API calls are permitted.

[Read the Air-Gapped Architecture](air-gapped.md)

## Cross-Cutting Concerns

Regardless of which architecture you adopt, the following concerns apply to all four patterns and are addressed within each architecture's design decisions:

- **Identity and access control** -- how users and services authenticate and receive authorization to invoke models.
- **Observability** -- how requests, responses, latencies, token counts, and errors are captured and surfaced.
- **Policy enforcement** -- how content filtering, rate limiting, cost controls, and data classification rules are applied.
- **Secret and credential management** -- how API keys, certificates, and tokens are stored, rotated, and scoped.
- **Cost attribution** -- how model consumption is tracked back to teams, projects, or business units.
