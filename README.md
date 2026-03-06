# DevOps Meta-Prompt

A structured protocol for operating software systems reliably after deployment — covering observability, SLOs, alerting, incident response, runbooks, and runtime security.

## What Is a Meta-Prompt?

A meta-prompt is a structured protocol document that initializes an AI-assisted development environment before task execution begins. Instead of starting with an ad-hoc prompt, the meta-prompt establishes workflow structure, behavioral constraints, and quality standards so that subsequent prompts operate inside a disciplined, well-defined system.

## This Meta-Prompt

DevOps governs everything that happens after software enters production. It covers:

- **Observability** — structured logging, metrics (request rate, error rate, latency, saturation), and distributed tracing correlated via trace ID
- **SLOs and Error Budgets** — defining SLIs, setting targets, calculating error budgets, and using burn-rate alerts
- **Monitoring and Alerting** — actionable alerts with severity labels, runbook links, and SLO burn-rate thresholds (not raw metric values)
- **Incident Response** — severity declaration, incident commander role, communication protocols, post-mortems
- **Reliability** — circuit breakers, health endpoints, chaos experiments, RTO/RPO documentation
- **Runbooks** — numbered-step operational procedures for every P1/P2 alert
- **Security Operations** — access auditing, time-limited privileged access, secret rotation, anomaly detection

This meta-prompt is invoked when systems approach production readiness or when operational processes need to be established or reviewed.

## Part of the Meta-Prompt Ecosystem

This meta-prompt works alongside companion protocols:

| Meta-Prompt | Repository | Purpose |
|---|---|---|
| LLM-Native Software Engineering | https://github.com/pragnakar/LLM_NATIVE_SOFTWARE_ENGINEERING | Architecture and application development |
| Deployment Engineering | https://github.com/pragnakar/Deployment_Engineering | Infrastructure, containerization, and deployment |
| DevOps | https://github.com/pragnakar/DevOps | Runtime operations, observability, and reliability |
| Database | https://github.com/pragnakar/Database | Data persistence, schema design, and management |
| UI/UX | https://github.com/pragnakar/UI-UX | Interface and experience design |

## Usage

1. Load `DevOps.md` into your AI coding agent when a system is approaching production or when operational processes need to be established
2. The agent follows the Principles, Practices, and Agent Instructions to generate structured logging, metrics middleware, health endpoints, alert rules, and runbooks
3. Use the Quick-Start Checklist to verify all operational baselines before a system accepts production traffic
4. Reference Integration Points to coordinate with Deployment Engineering (infrastructure) and Database (database health metrics)

## License

Open and collaborative. License information will be defined as the project evolves.

## Author

Pragnakar Pedapenki
