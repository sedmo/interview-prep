# Stephan Edmonson

**New York, NY | stephanedmonson@gmail.com | (973) 208-4065** | [LinkedIn](https://www.linkedin.com/in/stephan-edmonson/)

## Summary

Platform Engineer with a developer background, specialized in building "Golden Path" tooling and scalable infrastructure. Specialized in Kubernetes, Terraform, and AWS architecture, with strong coding skills (Go/Python) used to build shared libraries and automate internal developer platforms. Proven track record of reducing deployment friction, optimizing cloud costs (ECS vs. Lambda), and engineering reliability into complex distributed systems.

## Technical Skills

- **Infrastructure & Cloud:** AWS (ECS, Fargate, Lambda, IAM, VPC), Terraform (IaC), Kubernetes (EKS), Docker.
- **Platform Tooling:** GitHub Actions (CI), ArgoCD (GitOps), Helm.
- **Observability:** Prometheus, Grafana, Splunk, CloudWatch.
- **Automation Languages:** Go (Golang), Python, Bash.

## Professional Experience

### IBM Consulting | Senior Platform & DevOps Engineer | Remote | Mar 2024 – Feb 2026

*Deployed as a technical lead for high-profile clients including a Major Entertainment Company and Federal Government Agencies.*

**Client: Major Entertainment Company**

- Architected a centralized Infrastructure-as-Code solution using **Terraform**, enabling standardized deployment of event-driven workflows across multiple environments and eliminating configuration drift.
- Optimized compute resource allocation, analyzing workload patterns to migrate services between **ECS** and **Lambda**, balancing cold-start performance against cloud costs, **reducing monthly compute spend by ~15%** while stabilizing database connectivity.
- Orchestrated CI/CD pipelines using **GitHub Actions** and **Harness**, automating testing and deployment gates to reduce manual intervention and cut deployment time by **20%**.
- Implemented comprehensive observability, integrating **Splunk** and **CloudWatch** to monitor Kinesis stream lag (*IteratorAge*) and API health, proactively reducing MTTR.
- Refactored high-concurrency data migration scripts using **Go (goroutines)** and connection pooling, optimizing database throughput and reducing execution time by **40%**.

**Clients: Government Agencies**

- Modernized legacy infrastructure by designing secure AWS landing zones, ensuring compliance with strict governance standards for public sector workloads.
- Automated the provisioning of event-driven architecture (TS/Python on EventBridge, Lambda) using **Terraform** and **AWS CDK**, replacing aging mainframe processes with scalable cloud-native solutions, **reducing infrastructure provisioning time from days to minutes (<1 hour).**
- Established secure CI/CD patterns for hybrid cloud environments, enabling safe and auditable code delivery for mission-critical applications.

### Scale AI | DevOps Engineer (Contract) | Remote | Dec 2023 – Feb 2024

- Automated ML evaluation workflows, building tooling that allowed ML engineers to systematically test prompts and reduce manual testing cycles by **20%**.
- Integrated data-driven insights into production workflows, collaborating with engineering teams to stabilize model accuracy and reliability.
- Leveraged **BigQuery** to architect scalable analysis queries for monitoring model performance metrics across **TB-scale datasets**.

### The Knot Worldwide | Platform Engineer | Remote | May 2022 – Dec 2023

- Managed 5+ Kubernetes clusters, implementing **GitOps workflows (ArgoCD)** that reduced deployment failures by **30%** and ensured consistent cluster state.
- Provisioned scalable cloud infrastructure using **Terraform**, creating modular/reusable infrastructure components for AWS environments.
- Established comprehensive monitoring standards using **Prometheus** and **Grafana**, improving cluster visibility and enabling proactive incident response.
- Authored production runbooks and disaster recovery protocols, directly contributing to a **40%** reduction in incident resolution times.

### IBM | DevOps Engineer | New York, NY | Jan 2020 – Jun 2022

- Maintained **99.9% uptime** for a global sales platform serving 120K+ daily users through rigorous infrastructure management and on-call support.
- Managed **OpenShift** and **Docker** container environments, ensuring stable orchestration for critical production workloads.
- Integrated code quality gates (**SonarQube**) into the CI/CD pipeline, enforcing coding standards and improving test coverage by **20%** across the organization.

### IBM | Junior DevOps Engineer | New York, NY | Jan 2019 – Jan 2020

- Optimized defect triage workflows, leading a team of interns to build visualization tools that improved issue resolution speed by **25%**.
- Collaborated on global system integrations, ensuring data synchronization across international sales platforms.
- Standardized developer documentation for APIs, facilitating easier adoption and maintenance for the wider engineering team.

## Projects

- **Cloud-Native "GreenOps" Controller (Portfolio):** Developed a custom Kubernetes Controller in **Go** that monitors pod CPU utilization via **Prometheus** metrics. Implemented logic to automatically right-size or hibernate underutilized "zombie" pods, demonstrating proficiency in K8s internals, client-go, and cost-optimization automation.

## Education & Certifications

- **AWS Solutions Architect – Associate** (Issued Dec 2025)
- **B.S., Computer Science** | Brooklyn College (Jan 2019)
