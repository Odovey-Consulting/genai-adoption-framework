# Governance Readiness Checklist

This checklist validates that the governance layer --- Layer 1 of the GenAI Adoption Framework --- is ready to support enforcement at higher layers. Governance is the foundation of the entire framework. Nothing above it functions correctly without the policies, processes, and structures defined here.

Complete every applicable item before deploying the gateway (Layer 2). Items that are not yet applicable should be marked as such with documented justification and a target date for completion.

**Owner:** The governance lead or the designated executive sponsor for the GenAI program.

---

## Policy

- [ ] Acceptable Use Policy (AUP) has been drafted, reviewed by legal counsel, and approved by the executive sponsor.
- [ ] AUP has been distributed to all employees and contractors with access to generative AI systems.
- [ ] AUP acknowledgment has been collected from all personnel in scope (signature or electronic equivalent).
- [ ] Data classification tiers have been defined (e.g., public, internal, confidential, restricted) with clear criteria for each tier.
- [ ] Each data classification tier specifies which model deployment types are permitted (external API, hosted in private cloud, on-premises only, air-gapped).
- [ ] A model catalog has been published listing all approved models, their capabilities, known limitations, and approved use cases.
- [ ] The model catalog includes classification tier restrictions for each model (which tiers of data each model may process).
- [ ] A prohibited-use list has been defined specifying use cases that are explicitly not permitted regardless of classification tier.
- [ ] Policy review cadence has been established (recommended: quarterly at minimum) with a named owner for each policy document.

## Process

- [ ] A GenAI review board (or equivalent governance body) has been established with defined membership, charter, and meeting cadence.
- [ ] The use-case review and approval process has been documented, including intake form, review criteria, and approval authority.
- [ ] Roles and responsibilities for the GenAI program are documented and assigned (governance lead, platform team lead, security architect, cost owner).
- [ ] An incident escalation path has been defined for GenAI-specific incidents, including classification criteria, response timelines, and notification requirements.
- [ ] Exception request process has been documented for cases where a workload requires deviation from standard policy.
- [ ] Exception decisions are recorded with justification, approver identity, expiration date, and compensating controls.
- [ ] A workload onboarding process has been documented that includes completing a workload brief, assigning a classification tier, and identifying a human owner.

## Cost Governance

- [ ] Budget ownership for the GenAI program has been assigned to a named individual or team.
- [ ] An overall cost ceiling has been set for GenAI model consumption, with defined escalation thresholds (e.g., alert at 70%, escalate at 90%, hard stop at 100%).
- [ ] Per-team or per-business-unit cost allocations have been defined where applicable.
- [ ] A chargeback or showback model has been selected and documented (direct chargeback, shared cost pool, or hybrid).
- [ ] Cost reporting cadence has been established (recommended: weekly for platform team, monthly for business stakeholders).
- [ ] Cost forecasting methodology has been documented, including assumptions about usage growth and model pricing changes.

## Compliance

- [ ] Applicable regulatory requirements have been identified and mapped to framework layers (e.g., data residency requirements map to gateway deployment topology; audit requirements map to logging configuration).
- [ ] Regulatory obligations related to AI-specific legislation have been reviewed with legal counsel and documented.
- [ ] Audit evidence collection procedures have been documented, specifying what evidence is required, where it is stored, and who is responsible for producing it on demand.
- [ ] Data retention requirements have been defined for model interaction logs, including minimum and maximum retention periods.
- [ ] Third-party model provider agreements have been reviewed for data handling terms, and any gaps between provider terms and organizational policy have been documented and mitigated.
- [ ] A compliance review cadence has been established that aligns with the organization's existing audit cycle.

---

## Completion Criteria

All items in the Policy and Process sections must be complete before proceeding to gateway deployment. Cost Governance and Compliance items may be partially complete at the Foundation stage but must be fully resolved before entering the Operational maturity stage.

**Completed by:** ___________________________
**Date:** ___________________________
**Approved by:** ___________________________
