# Operations Anti-Patterns

> Layer 3 of the GenAI Adoption Framework

Anti-patterns are recurring approaches that appear reasonable but produce poor outcomes. Recognizing these patterns early prevents wasted effort and operational debt. Each anti-pattern described here has been observed in real generative AI platform deployments.

---

## 1. The Dashboard Cemetery

**Description:** The platform team builds dozens of dashboards during initial setup, covering every conceivable metric. Within weeks, nobody looks at most of them. Dashboards are never pruned, updated, or linked to operational workflows. They exist as artifacts of good intentions rather than tools for decision-making.

**Symptoms:**
- Dashboard count grows continuously but active viewer count remains flat or declines.
- When an incident occurs, engineers open a terminal and query logs directly rather than navigating to a dashboard.
- No dashboard has an owner, a review schedule, or a defined audience.

**Consequences:**
- Real signals are buried among irrelevant panels. Engineers lose confidence that dashboards reflect the current state of the system.
- Dashboard maintenance becomes a burden that is deferred indefinitely, leading to stale queries, broken data sources, and misleading visualizations.
- New team members cannot distinguish between important dashboards and abandoned ones, increasing onboarding confusion.

**Correct Approach:** Start with three to five dashboards, each tied to a specific operational workflow: incident triage, cost review, and adoption tracking. Every dashboard must have an owner, a defined audience, and a review cadence. Add new dashboards only when a specific operational need is identified and a human commits to using it. Retire dashboards that have not been viewed in 30 days.

---

## 2. Alert Fatigue

**Description:** The platform emits alerts for every metric deviation, threshold crossing, and anomaly. Alert volume overwhelms the on-call rotation. Engineers begin ignoring alerts, silencing channels, or creating filters that discard notifications. Eventually, a genuine incident goes unnoticed because it looks like every other alert.

**Symptoms:**
- On-call engineers acknowledge alerts without investigating them.
- Alert channels have been muted by most team members.
- The ratio of actionable alerts to total alerts is below 20 percent.

**Consequences:**
- Real incidents are missed or detected late because they are indistinguishable from noise.
- On-call morale degrades, leading to burnout and attrition on the platform team.
- Confidence in the alerting system erodes, and teams revert to manual monitoring during critical periods.

**Correct Approach:** Every alert must have a defined owner and a documented response action. If the response to an alert is "check if it resolves itself," the alert should be a dashboard metric, not a notification. Start with a small number of high-confidence alerts (error rate spike, cost burn rate exceeding threshold, upstream provider outage) and expand only after validating that existing alerts are acted upon consistently.

---

## 3. The Ticket Queue

**Description:** Obtaining an API key, onboarding a new team, or requesting access to a model requires submitting a ticket, obtaining manager approval, waiting for platform team review, and attending a scheduled onboarding meeting. The process takes days or weeks. Developers who need to move quickly find alternative paths that bypass the platform entirely.

**Symptoms:**
- Median time from access request to first successful API call exceeds 48 hours.
- Developers ask colleagues to share API keys rather than provisioning their own.
- Teams deploy direct integrations with model provider APIs to avoid the platform onboarding process.

**Consequences:**
- Shadow usage grows outside the platform's governance and observability perimeter. The organization loses visibility into generative AI spending, security posture, and compliance status.
- The platform team spends operational capacity processing tickets rather than improving the platform.
- Platform adoption metrics stagnate, undermining the business case for continued platform investment.

**Correct Approach:** Automate standard-tier access provisioning to be self-service and completable in minutes. Reserve manual approval workflows for elevated access tiers (access to restricted data, high-cost models, or production deployments). Measure onboarding time as a key operational metric and set a target: first successful API call within 15 minutes of starting the onboarding process.

---

## 4. Set and Forget

**Description:** Input and output guardrails are configured during initial platform deployment based on best-guess policies. After deployment, nobody reviews whether the guardrails are effective, whether false positive rates are acceptable, or whether new attack techniques have emerged. Guardrail configuration becomes frozen institutional knowledge.

**Symptoms:**
- No record of guardrail configuration changes after initial deployment.
- Developers complain about blocked requests, but no process exists to review and adjudicate their complaints.
- Adversarial testing has not been conducted since the platform launched.

**Consequences:**
- Guardrails drift out of alignment with organizational policy as policies evolve but configurations do not.
- False positives accumulate, eroding developer trust and incentivizing workarounds.
- New attack techniques bypass stale guardrail patterns, creating a false sense of security.

**Correct Approach:** Schedule weekly reviews of blocked requests to identify false positives and true positives. Conduct adversarial testing quarterly or after any significant platform change. Assign a guardrail owner responsible for tuning, testing, and communicating changes. Version-control all guardrail configurations and require review for changes, just as you would for application code.

---

## 5. The Second System

**Description:** The platform team builds a dedicated documentation site, separate from the organization's existing developer portal, internal wiki, or code repository documentation. The site is well-designed and comprehensive at launch but quickly falls out of date. Developers do not bookmark it, search engines do not index it, and it becomes an orphaned artifact.

**Symptoms:**
- Platform documentation exists on a different domain or system than all other internal documentation.
- Documentation traffic analytics show declining visits after the first month.
- Developers ask basic questions in support channels that are answered in the documentation, indicating they either cannot find it or do not know it exists.

**Consequences:**
- Documentation maintenance is duplicated: updates must be made in the dedicated site and often also communicated through messaging channels and meeting notes.
- New developers do not discover the documentation through their normal workflow. The platform appears undocumented to anyone who was not present at launch.
- The platform team loses motivation to maintain documentation that nobody reads, leading to a decay spiral.

**Correct Approach:** Publish platform documentation in the systems developers already use. Put getting-started guides in the code repository README. Put API references in the developer portal. Put troubleshooting tips in the support channel's pinned messages. Measure documentation effectiveness by tracking whether common support questions decrease after documentation updates.

---

## 6. Cost Surprise

**Description:** The platform has no budget alerts, no spend dashboards, or both exist but are not monitored. The organization discovers a cost overrun when the monthly invoice arrives, sometimes weeks after the spending occurred. By that point, the money is spent and the context for why it was spent has dissipated.

**Symptoms:**
- Cost discussions only happen during monthly or quarterly financial reviews.
- No team lead or budget owner can state their current month-to-date generative AI spend without consulting a billing console.
- The first indication of a runaway cost event is an email from finance, not an automated alert.

**Consequences:**
- Cost overruns are large because they are detected late. A runaway prompt loop that runs for a weekend is vastly more expensive than one caught within an hour.
- Trust between engineering and finance deteriorates. Finance teams impose blunt cost controls (hard spending caps, procurement freezes) because they lack confidence in engineering's ability to self-govern.
- Post-hoc cost attribution is difficult or impossible. Without real-time cost tracking, determining which team or workload caused the overrun requires forensic analysis of logs.

**Correct Approach:** Implement budget alerts at 50, 75, 90, and 100 percent of allocated budget. Route alerts to the budget owner for the consuming team. Display current spend on the primary operational dashboard. Implement automated throttling or hard stops at budget ceiling to prevent unbounded cost events. Conduct monthly cost reviews with all budget owners to discuss trends, anomalies, and forecasts.
