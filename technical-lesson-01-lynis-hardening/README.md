# Cloud Security Hardening Portfolio

Hardening an Ubuntu 22.04 web server with **Lynis** and **CIS Controls v8**, evaluating **IaaS / PaaS / SaaS** and **public / private / hybrid / community** deployment models, and planning **disaster recovery** for a client cloud migration.

> **Role:** Junior Cloud Security Consultant
> **Client:** Small e-commerce company moving from a local web server to the cloud
> **Status:** In progress

---

## Scenario

The client runs a local web server with performance bottlenecks, outdated security practices, and limited scalability. They are exploring cloud migration but are unsure which service model and deployment approach fit their business goals.

This repository documents the full consulting engagement: understanding requirements, hardening the existing server as a demonstration, evaluating cloud options, recommending a solution, and planning disaster recovery.

## Consulting Process

1. Understand customer challenges and requirements
2. Evaluate cloud service models (IaaS, PaaS, SaaS)
3. Explore deployment models (public, private, hybrid, community)
4. Develop a cloud solution recommendation
5. Plan disaster recovery

## Skills Demonstrated

- Linux system hardening (Ubuntu 22.04 LTS)
- Security auditing with Lynis
- Applying **CIS Controls v8 (Implementation Group 1)** and CIS Benchmarks (Ubuntu 22.04, Apache)
- Layered configuration: secure global defaults, documented exceptions only when necessary
- Cloud service and deployment model analysis
- Disaster recovery planning
- Technical documentation and client-facing recommendations

## Tools and Frameworks

| Tool / Framework | Purpose |
|---|---|
| Docker | Runs the assessment container (`nwendlo1/c4technicallesson`) |
| Ubuntu 22.04 LTS | Target operating system |
| Lynis | Security auditing and hardening assessment |
| Apache HTTP Server | Web server under assessment |
| CIS Controls v8 (IG1) | Foundational security safeguards |
| CIS Benchmarks | Detailed configuration guidance for Ubuntu 22.04 and Apache |
| Git / GitHub | Version control and documentation |

## Lesson Index

| # | Lesson | Focus | Status |
|---|---|---|---|
| 1 | [Securing Cloud Services Using Lynis](./technical-lesson-01-lynis-hardening/) | Lynis audit, CIS-based hardening, cloud model evaluation, DR planning | In progress |
| 2 | Technical Lesson 2 | TBD | Not started |
| 3 | Technical Lesson 3 | TBD | Not started |
| Final | [Final Lab](./final-lab/) | Capstone lab | Not started |

## Repository Structure

```
cloud-security-hardening-portfolio/
├── README.md
├── technical-lesson-01-lynis-hardening/
│   ├── README.md
│   ├── 01-customer-requirements.md
│   ├── 02-cloud-service-models.md
│   ├── 03-deployment-models.md
│   ├── 04-hardening/
│   │   ├── 00-environment-setup.md
│   │   ├── 01-baseline-lynis-audit.md
│   │   ├── 02-remediation-log.md
│   │   └── 03-post-hardening-audit.md
│   ├── 05-cloud-recommendation.md
│   ├── 06-disaster-recovery-plan.md
│   └── evidence/
├── final-lab/
└── docs/
```

## How to Read This Repo

Each lesson folder is self-contained. Start with the lesson's `README.md`, then follow the numbered files in order. Every remediation entry records the finding, the risk, the fix, the CIS reference, how it was verified, and how it maps to a cloud environment.

## Disclaimer

This is a learning and portfolio project performed in an isolated training container. No real client data or production systems were involved.

## Author

**captainx-dcode**
GitHub: [captainx-dcode](https://github.com/captainx-dcode)

## License

MIT