# Technical Incident Reports & Root Cause Analyses

An operations-focused incident knowledge base documenting how technical failures were investigated, isolated, corrected, and validated across **AWS, Linux, Docker, Jenkins, Git, Maven, Java, and application services**.

The incidents are drawn from real failures encountered while building and operating hands-on Cloud and DevOps environments.

They are **not presented as production outages**. Each report distinguishes the actual development-environment impact from hypothetical production, customer, security, or financial impact.

---

## Purpose

The goal of this repository is to demonstrate troubleshooting methodology rather than simply cataloging errors.

Each incident follows a consistent operational process:

```text id="se83kd"
Observe Failure
      ↓
Collect Evidence
      ↓
Identify Failing Layer
      ↓
Form Hypothesis
      ↓
Test Correction
      ↓
Validate Recovery
      ↓
Document Root Cause
      ↓
Define Preventive Actions
```

The emphasis is on **how the problem was investigated and proven**, not just the final command that fixed it.

---

## What This Repository Demonstrates

* evidence-driven fault isolation
* root-cause analysis
* Linux and application log investigation
* cloud and network troubleshooting
* container and service dependency analysis
* CI/CD troubleshooting
* authentication and authorization investigation
* recovery validation
* corrective and preventive action planning
* security-conscious evidence handling
* clear technical communication

---

## Incident Register

| Incident                                                                           | Technology                 | Failure                                                     | Status   |
| ---------------------------------------------------------------------------------- | -------------------------- | ----------------------------------------------------------- | -------- |
| [INC-20260717-DOCKER-01](incidents/INC-20260717-DOCKER-01-nexus-registry.md)       | Docker / Nexus / Linux     | Private registry connectivity and publication failure       | Resolved |
| [INC-20260804-DOCKER-01](incidents/INC-20260804-DOCKER-01-mongodb-connectivity.md) | Docker Compose / MongoDB   | Application used container-local `localhost` for MongoDB    | Resolved |
| [INC-202608-JENKINS-01](incidents/INC-202608-JENKINS-01-scm-retrieval.md)          | Jenkins / GitHub           | Pipeline and Shared Library retrieval failures              | Resolved |
| [INC-202608-MAVEN-01](incidents/INC-202608-MAVEN-01-source-packaging.md)           | Maven / Spring Boot        | Source discovery and application packaging failure          | Resolved |
| [INC-202608-JENKINS-02](incidents/INC-202608-JENKINS-02-shared-library.md)         | Jenkins / Groovy           | Shared Library classpath and runtime resolution failure     | Resolved |
| [INC-202608-DOCKER-01](incidents/INC-202608-DOCKER-01-socket-permissions.md)       | Docker / Linux / Jenkins   | Docker socket permission failure                            | Resolved |
| [INC-202608-DOCKER-02](incidents/INC-202608-DOCKER-02-registry-authorization.md)   | Docker Hub / Jenkins       | Registry namespace authorization failure                    | Resolved |
| [INC-202608-DOCKER-03](incidents/INC-202608-DOCKER-03-image-build-push.md)         | Docker / Jenkins / Groovy  | Pipeline attempted to push an image that had not been built | Resolved |
| [INC-202608-GIT-01](incidents/INC-202608-GIT-01-detached-head-writeback.md)        | Git / Jenkins / GitHub     | Detached-HEAD automated push failure                        | Resolved |
| [INC-20260901-AWS-01](incidents/INC-20260901-AWS-01-ec2-spring-deployment.md)      | AWS / Docker / Spring Boot | Multi-stage EC2 application deployment failure              | Resolved |

---

## Example Failure Domains

The incidents span multiple layers of the application-delivery stack.

```text id="5jj9o6"
Source Control
      ↓
Git / GitHub / GitLab
      ↓
CI/CD
      ↓
Jenkins / Groovy
      ↓
Build
      ↓
Maven / Java
      ↓
Containerization
      ↓
Docker
      ↓
Artifact / Registry
      ↓
Nexus / Docker Hub
      ↓
Cloud Infrastructure
      ↓
AWS / Linux
      ↓
Application Runtime
```

This makes it possible to demonstrate troubleshooting across system boundaries rather than within a single technology.

---

## Incident Methodology

Each report is structured to answer the same operational questions.

### 1. What failed?

The visible symptom, error message, unavailable service, failed build, or unexpected system behavior.

### 2. What was the actual impact?

The affected environment and what functionality was unavailable.

### 3. What evidence was available?

Examples include:

* Jenkins Console Output
* application logs
* Docker logs
* service status
* Git state
* HTTP responses
* filesystem state
* build output
* network behavior

### 4. Which layer was failing?

The investigation narrows the issue across layers such as:

```text id="6jxkdq"
Network
Authentication
Authorization
Source Control
Build
Container
Runtime
Application
```

### 5. What was the root cause?

The underlying technical condition responsible for the failure.

### 6. What corrected it?

The smallest corrective action that resolved the identified cause.

### 7. How was recovery validated?

The same or stronger test that originally exposed the problem is repeated to confirm recovery.

### 8. What would reduce recurrence?

Where applicable, the report identifies corrective or preventive actions.

---

## Report Structure

Each incident report includes:

1. Incident metadata
2. Executive summary
3. Impact assessment
4. Symptoms and evidence
5. Investigation
6. Root cause
7. Resolution and recovery
8. Validation
9. Corrective and preventive actions
10. Lessons and technical capabilities demonstrated

---

## Incident ID Convention

Incident IDs follow:

```text id="2ynayq"
INC-YYYYMMDD-TECHNOLOGY-NN
```

Example:

```text id="x1etj9"
INC-20260901-AWS-01
```

If the exact day was not preserved, the verified month is used:

```text id="i34pbk"
INC-YYYYMM-TECHNOLOGY-NN
```

Dates are not reconstructed from the date the report was written.

---

## Environment and Impact Disclosure

The incidents in this repository occurred in **portfolio development environments**.

Severity and impact statements are based only on what actually occurred.

The reports do not invent:

* production outages
* customer impact
* SLA violations
* financial losses
* security breaches
* unavailable timestamps
* business impact that was not observed

This keeps the repository technically credible while still documenting the same troubleshooting disciplines used in operational environments.

---

## Evidence Policy

Evidence is included only when it was actually preserved.

Examples may include:

* screenshots
* command output
* logs
* build results
* HTTP responses
* repository state
* container state

Evidence is reviewed before publication to avoid exposing:

* passwords
* API tokens
* Personal Access Tokens
* private keys
* authenticated Git URLs
* sensitive credentials
* unnecessary public IP addresses
* other security-sensitive information

If evidence was not preserved, it is not recreated for the report.

The investigation instead documents the commands, behavior, or observations that were used at the time.

---

## Validation Standard

An incident is not considered resolved simply because an error message disappears.

Recovery should demonstrate that the affected workflow works again.

Examples include:

```text id="a44192"
Failed Jenkins Build
        ↓
Correction
        ↓
Successful Jenkins Build

Failed Docker Push
        ↓
Correction
        ↓
Image Verified in Registry

Application Unavailable
        ↓
Correction
        ↓
HTTP 200 / Expected Response

Database Connectivity Failure
        ↓
Correction
        ↓
Application-to-Database Operation Verified
```

This helps distinguish a configuration change from a validated resolution.

---

## Troubleshooting Principles

Several principles appear repeatedly across the reports.

### Treat Error Messages as Evidence

An error often identifies which layer of the system has already been reached successfully.

For example, an application-generated `404` may indicate that networking and container routing are already functioning.

### Change One Layer at a Time

Corrections are targeted at the layer supported by the available evidence rather than making broad configuration changes.

### Validate Outside the Failing Tool

Where practical, results are verified independently.

Examples include:

* verifying a Docker image directly in the registry
* checking the generated JAR
* testing an HTTP endpoint directly
* inspecting container state
* confirming repository changes in GitHub

### Distinguish Symptoms from Root Cause

A failed deployment may surface as a networking issue while the actual cause exists in application packaging, permissions, or configuration.

---

## Relationship to Project Repositories

These incident reports are not substitutes for the project documentation.

The project repositories explain:

* architecture
* implementation
* engineering decisions
* overall outcomes

This repository focuses specifically on:

```text id="yaej25"
Failure
   ↓
Investigation
   ↓
Root Cause
   ↓
Recovery
```

Where appropriate, incident reports link back to the project where the failure occurred.

---

## Documentation Transparency

The incidents are based on troubleshooting performed during hands-on technical projects.

AI-assisted tools were used to help organize notes, improve readability, and standardize documentation structure.

The underlying:

* errors
* commands
* investigation steps
* technical evidence
* root causes
* corrective actions
* validation results

reflect work performed and reviewed by the repository author.

AI-assisted documentation was not used to fabricate incidents, evidence, production impact, or customer impact.

---

## Technologies Represented

Current reports include work involving:

* AWS EC2
* Linux
* Docker
* Docker Compose
* MongoDB
* Sonatype Nexus
* Jenkins
* Jenkins Shared Libraries
* Groovy
* Git
* GitHub
* Maven
* Java
* Spring Boot
* Docker Hub

The repository will continue to grow only when an incident provides meaningful troubleshooting value.

---

## Engineering Outcome

The purpose of this repository is to show that troubleshooting is more than identifying the command that eventually worked.

The reports demonstrate a repeatable operational approach:

```text id="x677nm"
Understand the System
        ↓
Use Evidence
        ↓
Narrow the Failure Domain
        ↓
Identify Root Cause
        ↓
Apply a Targeted Correction
        ↓
Validate Recovery
        ↓
Document the Result
```

That approach is transferable across **Cloud Support, Cloud Operations, Application Support, DevOps, and production-support environments**.

---

## Related Portfolio

The implementations behind these incidents are documented in the [Cloud & DevOps Engineering Portfolio](https://github.com/Ejones904/cloud-devops-portfolio).

---

## Author

**Ethan Jones**

Cloud Support / Cloud Operations / Application Support

[GitHub](https://github.com/Ejones904) · [LinkedIn](https://www.linkedin.com/in/ethanjones-jacksonville)


