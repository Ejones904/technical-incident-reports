# Technical Incident Reports & Root Cause Analyses

This repository is an operations-focused knowledge base documenting how I investigate, isolate, resolve, and validate technical failures across cloud infrastructure, Linux, Docker, Jenkins, Git, Maven, Java, and application services.

The reports are based on issues encountered during hands-on Cloud and DevOps projects. They are not presented as production outages. Each report clearly distinguishes development-environment impact from production, customer, data, and security impact.

## What This Repository Demonstrates

- Evidence-driven fault isolation
- Application and infrastructure log analysis
- Root-cause analysis across multiple technology layers
- Recovery and end-to-end validation
- Security-conscious credential and permission handling
- Corrective and preventive action planning
- Clear operational documentation

## Incident Register

| Incident | Technology | Primary failure | Status |
| --- | --- | --- | --- |
| [INC-20260717-DOCKER-01](incidents/INC-20260717-DOCKER-01-nexus-registry.md) | Docker / Nexus / Linux | Private registry connectivity and publication failure | Resolved |
| [INC-20260804-DOCKER-01](incidents/INC-20260804-DOCKER-01-mongodb-connectivity.md) | Docker Compose / MongoDB | Application used container-local `localhost` for MongoDB | Resolved |
| [INC-202608-JENKINS-01](incidents/INC-202608-JENKINS-01-scm-retrieval.md) | Jenkins / GitHub | Pipeline and Shared Library retrieval failures | Resolved |
| [INC-202608-MAVEN-01](incidents/INC-202608-MAVEN-01-source-packaging.md) | Maven / Spring Boot | Source discovery and application packaging failure | Resolved |
| [INC-202608-JENKINS-02](incidents/INC-202608-JENKINS-02-shared-library.md) | Jenkins / Groovy | Shared Library classpath and runtime resolution failure | Resolved |
| [INC-202608-DOCKER-01](incidents/INC-202608-DOCKER-01-socket-permissions.md) | Docker / Linux / Jenkins | Docker socket permission failure | Resolved |
| [INC-202608-DOCKER-02](incidents/INC-202608-DOCKER-02-registry-authorization.md) | Docker Hub / Jenkins | Registry namespace authorization failure | Resolved |
| [INC-202608-DOCKER-03](incidents/INC-202608-DOCKER-03-image-build-push.md) | Docker / Jenkins / Groovy | Pipeline pushed an image that had not been built | Resolved |
| [INC-202608-GIT-01](incidents/INC-202608-GIT-01-detached-head-writeback.md) | Git / Jenkins / GitHub | Detached-HEAD automated push failure | Resolved |
| [INC-20260901-AWS-01](incidents/INC-20260901-AWS-01-ec2-spring-deployment.md) | AWS / Docker / Spring Boot | Multi-stage EC2 application deployment failure | Resolved |

## Report Structure

Each report contains:

1. Incident metadata
2. Executive summary
3. Impact assessment
4. Symptoms and evidence
5. Investigation
6. Root cause
7. Resolution and recovery
8. Validation
9. Corrective and preventive actions
10. Lessons learned and skills demonstrated

## Incident ID Convention

```text
INC-YYYYMMDD-TECHNOLOGY-NN
```

When the exact incident day is not preserved, the verified month is used:

```text
INC-YYYYMM-TECHNOLOGY-NN
```

Dates are never inferred from the day a report was written. Reports state when an exact timestamp was not preserved.

## Environment and Impact Disclosure

These incidents occurred in portfolio development environments. Severity is based on observed impact, not technical complexity. The reports do not invent customers, production outages, financial impact, or unavailable timestamps.

## Evidence Policy

Screenshots and log excerpts are included only when they were preserved and can be shown without exposing credentials, tokens, public IP addresses, authenticated remote URLs, or other sensitive information. The absence of a screenshot is never replaced with reconstructed evidence. Each report instead identifies the commands, outputs, or validation behavior that supported the investigation.

## Related Portfolio

The implementations behind these incidents are documented in my [Cloud & DevOps Engineering Portfolio](https://github.com/Ejones904/cloud-devops-portfolio).

## Author

**Ethan Jones**

Cloud Support / Cloud Operations / Application Support

[GitHub](https://github.com/Ejones904) · [LinkedIn](https://www.linkedin.com/in/ethanjones-jacksonville)
