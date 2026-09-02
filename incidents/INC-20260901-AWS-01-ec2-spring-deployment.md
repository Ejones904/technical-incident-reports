# INC-20260901-AWS-01 — Containerized Spring Boot Deployment Failure on AWS EC2

## Incident Metadata

| Field | Details |
| --- | --- |
| Status | Resolved |
| Classification | Cloud application deployment incident |
| Date | September 1, 2026 |
| Environment | AWS EC2, Amazon Linux, Docker, Java/Spring Boot, Maven |
| Detection | HTTP responses, container logs, and JAR inspection |
| Owner | Ethan Jones |

## Executive Summary

A containerized Spring Boot application deployed to AWS EC2 failed to provide the expected service. The investigation progressed through three evidence-driven stages:

1. HTTP 404 isolated the failure to missing Spring MVC routing.
2. Corrected source produced unchanged behavior because the Maven artifact was stale.
3. The container later exited with `ClassNotFoundException` because the Java package and expected main-class path did not match.

The source, Maven artifact, Docker image, and EC2 container were corrected and rebuilt in sequence. Recovery was validated locally from EC2 and externally through the public endpoint with HTTP 200 and `OK`.

## Impact Assessment

| Impact area | Details |
| --- | --- |
| Technical impact | The EC2-hosted application could not provide the expected route and later became unavailable when the Java process exited. |
| Operational impact | Deployment validation and application availability testing were blocked. |
| Scope | One EC2 instance, Docker container, Spring Boot application, and artifact chain. |
| Production impact | None — development environment |
| Customer impact | None |
| Data impact | No data loss, corruption, or integrity impact identified. |
| Security impact | No unauthorized access or credential exposure identified. |

## Architecture

```text
External client
      ↓
AWS Security Group
      ↓
EC2 public interface :3000
      ↓
Docker mapping 3000:8080
      ↓
Embedded Tomcat :8080
      ↓
Spring Boot application
```

## Investigation Stage 1 — HTTP 404

`docker ps` showed:

```text
0.0.0.0:3000->8080/tcp
```

Tomcat and Spring Boot startup appeared successful, but the public endpoint returned a Spring Boot Whitelabel HTTP 404. Because Spring Boot generated the response, the request had already crossed the Security Group, EC2, Docker mapping, and Tomcat. The investigation moved to application routing.

The application contained `getStatus()` but did not expose `GET /`.

### Corrective Action

```java
@RestController

@GetMapping("/")
public String getStatus() {
    return "OK";
}
```

## Investigation Stage 2 — Stale Maven Artifact

The corrected annotations existed in source, but deployed behavior initially remained unchanged. The source-to-runtime chain was examined:

```text
Java source → Maven JAR → Docker image → Docker Hub → EC2 container
```

The Docker build still contained an older JAR. The Maven artifact was rebuilt before another image was created.

## Investigation Stage 3 — `ClassNotFoundException`

The symptom changed from an HTTP response to a connection failure. Docker inspection and logs showed that the Java process had exited with `ClassNotFoundException`.

The expected class was `com.example.Application`. The source path, `package com.example;` declaration, Spring Boot main-class configuration, and packaged class location were aligned. The rebuilt JAR was inspected for:

```text
BOOT-INF/classes/com/example/Application.class
```

## Root Cause

Three application and artifact defects were revealed sequentially:

1. Missing Spring MVC route
2. Stale Maven artifact
3. Java package and main-class mismatch

AWS networking and Docker port mapping were not the primary causes.

## Resolution

```text
Correct route
  → Align Java package and path
  → Rebuild Maven artifact
  → Inspect JAR
  → Build versioned image
  → Push to Docker Hub
  → Replace EC2 container
  → Validate locally
  → Validate externally
```

## Validation

- EC2-local request returned `HTTP/1.1 200` and `OK`.
- External request to `http://<EC2-PUBLIC-IP>:3000/` returned `HTTP/1.1 200` and `OK`.
- The final test verified the complete path through AWS, EC2, Docker, Tomcat, and Spring Boot.

## Corrective and Preventive Actions

| Action | Type | Status |
| --- | --- | --- |
| Add the missing Spring route | Corrective | Completed |
| Align package, source path, and main class | Corrective | Completed |
| Rebuild and inspect the Maven artifact | Corrective/validation | Completed |
| Build and publish a new Docker image | Corrective | Completed |
| Validate locally before external testing | Validation | Completed |
| Add route and packaging tests to CI | Preventive | Recommended |
| Use immutable Docker image tags | Preventive | Recommended |
| Add a container health check | Preventive | Recommended |

## Lessons Learned

- An application-generated HTTP response identifies how far a request traveled.
- HTTP 404 and connection refusal represent different failure domains.
- Source code, build artifact, image, and running container are separate versions.
- Logs and artifact inspection should precede unnecessary infrastructure changes.

## Skills Demonstrated

AWS EC2, Security Groups, Linux deployment, Docker inspection and networking, HTTP analysis, Spring Boot/Tomcat, Java classpath troubleshooting, Maven artifact validation, log analysis, RCA, and end-to-end validation.

## Final Status

**Resolved and validated.** The application returned HTTP 200 and `OK` locally and externally.
