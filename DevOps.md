# DevOps

## A Meta-Prompt for Runtime Operations, Observability, and System Reliability

**Version:** 1.0
**Repository:** https://github.com/pragnakar/DevOps
**Parent Meta-Prompt:** LLM-Native Software Engineering — https://github.com/pragnakar/LLM_NATIVE_SOFTWARE_ENGINEERING
**Companion Meta-Prompts:** Deployment Engineering, Database, UI-UX

---

## Thesis

Building and deploying software is the beginning, not the end. Production systems degrade, fail, and behave unexpectedly under real load. DevOps ensures that when failures occur, they are detected immediately, impact is minimized, and root causes are understood. This meta-prompt shifts focus from construction to sustained operation — keeping systems observable, reliable, and recoverable throughout their operational lifecycle.

## When This Meta-Prompt Is Invoked

Invoke this meta-prompt when:
- A system is approaching production deployment or has entered production
- Monitoring, alerting, or on-call procedures need to be established or reviewed
- An incident has occurred and operational processes need to be assessed
- SLOs, SLIs, or error budgets need to be defined
- Operational runbooks need to be written or updated
- A post-mortem process needs to be established

## Scope

**This meta-prompt covers:**
- Monitoring system health and performance: metrics, dashboards, SLOs/SLIs
- Log collection, aggregation, analysis, and anomaly detection
- Alerting: thresholds, routing, escalation policies
- Incident detection, response, and post-mortem processes
- Reliability engineering: redundancy, failover, graceful degradation, error budgets
- On-call processes, runbooks, and operational documentation
- Runtime security operations: access auditing, secret rotation, vulnerability monitoring

**This meta-prompt does NOT cover:**
- Provisioning infrastructure or defining deployment pipelines — handled by **Deployment Engineering**
- Database schema design or migration — handled by the **Database** meta-prompt
- Application architecture and build workflow — handled by **LLM-Native Software Engineering**
- User interface design — handled by the **UI-UX** meta-prompt

## Principles

1. **Failures are inevitable; undetected failures are not acceptable.** Every production system will fail. The only question is how quickly the failure is detected and how much user impact occurs before recovery. Detection speed is the primary reliability lever.

2. **Observability is designed in, not bolted on.** Systems emit structured logs, metrics, and traces by design — not retrofitted after the first production incident reveals a gap. If the data isn't there when you need it, you cannot get it after the fact.

3. **Every alert must be actionable.** An alert without a defined response action is noise. Alerts that fire and require no action are eliminated or downgraded. Alert fatigue kills on-call effectiveness.

4. **SLOs define the acceptable degradation boundary.** Service Level Objectives are set before production launch and drive alert thresholds. Error budgets determine whether new feature work or reliability investment takes priority at any given time.

5. **Runbooks exist before incidents occur.** Every P1 and P2 alert has a corresponding runbook. If an incident requires improvisation, a runbook is written before the incident is closed. Improvisation should only happen once.

6. **Least-privilege access is enforced at the operational layer.** Production access is role-based, audited, and time-limited where possible. Persistent unrestricted access to production systems is not permitted.

7. **Post-mortems are blameless and mandatory.** Every severity-1 and severity-2 incident produces a written post-mortem. Root causes are structural or systemic — not personal. The goal is durable fixes, not accountability.

## Practices

### Observability Stack

- Emit structured logs (JSON) from all services. Every log entry includes: timestamp, severity, service name, trace ID, and relevant operational context.
- Instrument all services with a metrics client (Prometheus, Datadog, CloudWatch). Expose at minimum: request rate, error rate, latency (p50/p95/p99), and resource saturation.
- Implement distributed tracing (OpenTelemetry) across all service boundaries. Trace IDs propagate through all inter-service calls and are present in logs and metrics.
- Correlate all three signals: a single trace ID links a metric anomaly to the relevant log entries and traces. Uncorrelated observability data forces manual correlation under pressure.

### SLOs and Error Budgets

- Define SLIs (measurable indicators) before production launch. Example: "percentage of HTTP requests returning 2xx within 500ms."
- Set SLOs (targets) for each SLI. Example: "99.5% of requests complete successfully within 500ms, measured over a rolling 28-day window."
- Calculate error budget: `error budget = (1 - SLO target) × time window`. When error budget is exhausted, freeze new feature work and prioritize reliability.
- Review SLO compliance weekly. SLOs never violated should be tightened. SLOs consistently violated require immediate engineering investment.

### Monitoring and Alerting

- Alert on SLO burn rate, not raw metric values. A burn-rate alert fires when error budget is being consumed faster than the SLO window permits — catching both fast burns (p1 alert) and slow burns (p2 alert).
- Every alert rule includes: name, severity (P1/P2/P3), impact statement, and runbook URL.
- P1 alerts page on-call immediately. P2 alerts notify within 15 minutes. P3 alerts are tracked in a queue for next business day.
- Review alert noise monthly. Any alert that fires more than twice without requiring a human response is recalibrated or removed.
- Dashboards are organized by user journey, not by infrastructure component. An engineer responding to an alert should see user impact first, infrastructure metrics second.

### Incident Response

- Declare incident severity immediately upon detection. Do not wait to understand full scope before declaring.
- Assign an incident commander for all P1/P2 incidents. The commander owns communication, not diagnosis.
- Open a dedicated communication channel (Slack, incident.io, PagerDuty) for every P1 incident.
- Response time targets: detection-to-acknowledgment within 5 minutes (P1), mitigation within 30 minutes (P1).
- Rollback is always the first mitigation option evaluated before attempting a forward fix under pressure.
- Write and publish a post-mortem within 48 hours of P1/P2 incident resolution.

### Reliability Practices

- Design for failure: any dependency can fail at any time. Implement timeouts, retries with exponential backoff and jitter, and circuit breakers on all external calls.
- Implement health endpoints (`/health`, `/ready`) on all services. Load balancers and orchestrators use these for routing and restart decisions.
- Conduct quarterly failover and chaos experiments to validate that recovery mechanisms function as documented — not just as designed.
- Document Recovery Time Objective (RTO) and Recovery Point Objective (RPO) for each service. Validate against actual recovery drills, not theoretical estimates.

### Runbooks

- Every alert links to a numbered-step runbook. Runbooks are not prose — they are executable decision trees.
- Every runbook includes: what the alert means, probable causes, diagnostic commands, mitigation steps, and escalation criteria.
- Update runbooks every time an incident reveals a gap or inaccuracy. A stale runbook is more dangerous than no runbook — it gives false confidence.

### Security Operations

- All production access is logged and audited. Access logs are retained for a minimum of 90 days.
- Privileged access (SSH, `kubectl exec`, database admin queries) is time-limited and approval-gated where infrastructure supports it.
- Rotate secrets on a defined schedule. Verify rotation by testing with new credentials before revoking old ones.
- Monitor for anomalous access patterns: off-hours privileged logins, unusual API call volumes, access to sensitive resources outside normal operational patterns.

## Agent Instructions

When an LLM coding agent is operating under this meta-prompt:

**Do:**
- Generate structured JSON log statements in all service code, including `service`, `trace_id`, and `severity` fields
- Include `/health` and `/ready` endpoints in every service scaffold
- Generate Prometheus metrics middleware (or equivalent) for all HTTP/gRPC services
- Generate alert rule definitions with severity labels and `runbook_url` annotations
- Write runbooks as numbered step sequences with diagnostic and mitigation steps clearly separated
- Generate circuit breaker and retry logic (with exponential backoff) for all external service calls
- Generate SLO alert rules using multi-window burn rate, not static thresholds

**Do NOT:**
- Generate alert thresholds based on arbitrary numeric values — alert on SLO burn rate
- Write runbooks as prose paragraphs — they must be numbered steps executable under incident conditions
- Generate production access configurations with persistent unrestricted admin roles
- Skip health endpoint generation for any long-running service
- Generate observability configurations that do not correlate logs, metrics, and traces via trace ID
- Define infrastructure or deployment configuration — that is Deployment Engineering's domain

**Verify before proceeding:**
- Does every service emit structured logs with a `trace_id` field?
- Does every service expose `/health` and `/ready` endpoints?
- Does every generated alert rule include a `runbook_url` annotation?
- Are there circuit breakers or retry policies on all external service calls?
- Is production access role-based and audited?

## Integration Points

- **LLM-Native Software Engineering:** DevOps operates on systems built via the LLM-Native SE protocol. Verification prompts defined in LLM-Native SE can evolve into CI pipeline stages that also feed operational monitoring. The phase structure and build log inform runbook context and incident scope.

- **Deployment Engineering:** Deployment Engineering provisions the infrastructure; DevOps monitors and operates it. The logging pipelines, metrics collectors, and tracing agents provisioned by Deployment Engineering are the data sources DevOps consumes. Deployment Engineering defines the rollback mechanism; DevOps defines the SLO burn-rate condition that triggers it.

- **Database:** DevOps monitors database health metrics: query latency, connection pool saturation, replication lag, lock wait time. Database-specific runbooks address query performance degradation, replication failures, and backup validation. Database maintenance windows are coordinated with DevOps on-call to avoid alert noise.

- **UI-UX:** Frontend performance metrics (Core Web Vitals: LCP, FID, CLS) are monitored as part of DevOps observability. User-facing SLOs are defined in terms that align with UX quality standards. Frontend JavaScript errors and failed API calls are tracked in the operational monitoring stack.

## Anti-Patterns

| Anti-Pattern | Why It's Harmful |
|---|---|
| Alerting on raw metric values instead of SLO burn rate | Generates noise at safe operating levels and misses slow-burning SLO violations that will exhaust the error budget before triggering |
| Writing runbooks as prose paragraphs | Engineers reading under incident pressure cannot quickly locate the next action; narrative format increases mean time to recover |
| Alert fatigue — too many low-signal alerts | On-call engineers begin ignoring or silencing alerts; a real P1 is missed in the noise |
| Post-mortem blame culture | Engineers hide incidents or delay escalation; structural root causes recur because they are never surfaced |
| Persistent unrestricted admin access to production | Violates least-privilege; access logs become meaningless; any credential compromise has maximum blast radius |
| Retrofitting observability after a production incident | The incident that reveals the observability gap is the one you cannot diagnose; structured logging must precede production launch |
| Manual incident response without runbooks | Response quality and speed vary by who is on-call; institutional knowledge is lost when team members change |
| Treating SLOs as aspirational targets rather than operational constraints | SLOs without error budget enforcement are metrics that get reported, not acted upon; they have no operational effect |

## Quick-Start Checklist

```
[ ] Define SLIs for each user-facing service
[ ] Set SLO targets and calculate error budgets
[ ] Configure structured JSON logging with trace_id in all services
[ ] Deploy metrics collection (Prometheus/Datadog/equivalent)
[ ] Configure distributed tracing (OpenTelemetry)
[ ] Create dashboards organized by user journey
[ ] Write SLO burn-rate alert rules with severity labels and runbook URL annotations
[ ] Configure alert routing (PagerDuty, OpsGenie, or equivalent)
[ ] Implement /health and /ready endpoints on all services
[ ] Write runbooks for every P1/P2 alert (numbered steps, not prose)
[ ] Define incident severity levels and response SLAs
[ ] Assign on-call rotation and escalation paths
[ ] Establish post-mortem process and template
[ ] Schedule quarterly failover and chaos drills
[ ] Configure production access as role-based and audited
[ ] Verify all three signals (logs, metrics, traces) are correlated via trace ID
```

## Version History

- v1.0 — 2026-03-06 — Initial harmonization: full restructure to canonical meta-prompt template, added Principles, Practices, Agent Instructions, Integration Points, Anti-Patterns, and Quick-Start Checklist

---

*Part of the Meta-Prompt Ecosystem: https://github.com/pragnakar*
