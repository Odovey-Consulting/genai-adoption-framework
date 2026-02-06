# Workload Release Checklist

This checklist validates that an individual workload --- Layer 4 of the GenAI Adoption Framework --- is ready for production deployment. Unlike the previous checklists, which are completed once per platform, this checklist is completed once per workload. Every workload that goes to production must pass through this gate independently.

Complete every applicable item before promoting the workload to production. The Operations Readiness checklist must be complete for the platform on which the workload will run; a workload cannot be production-ready on a platform that is not.

**Owner:** The workload's designated human owner, as identified in the workload brief.

**Prerequisite:** [Operations Readiness Checklist](operations-launch.md) --- complete (platform-level).

---

## Scoping and Governance

- [ ] A workload brief has been completed and submitted through the use-case review process defined in governance.
- [ ] The workload has been reviewed and approved by the governance review board (or equivalent approval authority).
- [ ] A data classification tier has been assigned to the workload, and the assigned tier is consistent with the data the workload will process.
- [ ] Success criteria have been defined in measurable terms (e.g., accuracy threshold, latency target, user satisfaction score, cost per transaction).
- [ ] A cost ceiling has been set for this specific workload, with alerting thresholds configured in the platform's cost management system.
- [ ] A human owner has been identified and recorded in the system of record; the owner has acknowledged their accountability for this workload.
- [ ] If the workload processes data from multiple classification tiers, the highest applicable tier governs the workload's controls.
- [ ] An exit or decommission criteria has been defined: the conditions under which this workload should be retired or significantly restructured.

## Quality and Evaluation

- [ ] A golden dataset has been created with a minimum of 30 representative examples covering the workload's expected input distribution, including edge cases.
- [ ] The golden dataset is version-controlled and stored alongside the workload's other artifacts.
- [ ] An evaluation scorecard has been defined with specific metrics, thresholds, and pass/fail criteria aligned with the success criteria from the workload brief.
- [ ] Automated evaluation runs against the golden dataset as part of the continuous integration pipeline.
- [ ] The current evaluation results meet or exceed all defined thresholds; any exceptions are documented with justification and a remediation plan.
- [ ] Prompt templates are version-controlled with change history and meaningful commit messages.
- [ ] Prompt regression testing is included in the CI pipeline: changes to prompt templates trigger evaluation runs automatically.

## Security and Adversarial Testing

- [ ] An adversarial test suite has been created targeting the workload's specific attack surface (prompt injection, jailbreak attempts, data extraction, output manipulation).
- [ ] The adversarial test suite includes a minimum of 20 test cases, covering known attack patterns relevant to the workload's function and data sensitivity.
- [ ] Adversarial tests run as part of the continuous integration pipeline and must pass before deployment proceeds.
- [ ] Prompt injection tests are included that attempt to override system instructions through user input, retrieved context, and tool outputs.
- [ ] Output validation rules are defined and active: the workload checks model outputs for format compliance, safety, and policy adherence before serving them to users or downstream systems.
- [ ] The workload has been tested against the DLP rules configured at the gateway to confirm that sensitive data in model responses is properly handled.

## Deployment and Integration

- [ ] The workload routes all model traffic through the gateway; no direct connections to model provider endpoints exist.
- [ ] Gateway integration has been verified: the workload authenticates correctly, routes to the approved model, and respects rate limits and content filters.
- [ ] Observability is configured for this workload: request logs, latency metrics, error rates, and token consumption are visible in the platform's dashboards.
- [ ] Classification tier enforcement has been confirmed: the gateway applies the correct content filters and DLP rules based on the workload's assigned classification tier.
- [ ] The deployment process is automated and repeatable (infrastructure as code, CI/CD pipeline, or equivalent); manual deployment steps are documented if any remain.
- [ ] A rollback procedure has been tested: the workload can be reverted to the previous known-good version within the defined recovery time objective.

## Operations and Monitoring

- [ ] Production monitoring is configured with workload-specific alerts for: error rate increase, latency degradation, quality score drop (if continuous evaluation is active), and cost anomalies.
- [ ] Quality sampling is scheduled: a defined percentage of production interactions are reviewed by humans on a regular cadence (recommended: weekly during the first month, then adjusted based on stability).
- [ ] A feedback loop is defined: mechanisms exist for end users, reviewers, or downstream systems to flag problematic outputs, and flagged items are reviewed within a defined timeframe.
- [ ] Escalation procedures specific to this workload are documented: who to contact when the workload produces harmful, incorrect, or unexpected outputs.
- [ ] Performance baselines have been recorded from pre-production evaluation so that production behavior can be compared against expected behavior.
- [ ] A post-launch review is scheduled (recommended: two weeks after deployment) to assess whether the workload meets its defined success criteria in production.

---

## Completion Criteria

All items in Scoping, Security, and Deployment must be complete before production launch. Quality items must be complete with all evaluation thresholds met. Operations items must be configured and verified before launch, with the post-launch review scheduled.

**Completed by:** ___________________________
**Date:** ___________________________
**Approved by:** ___________________________
