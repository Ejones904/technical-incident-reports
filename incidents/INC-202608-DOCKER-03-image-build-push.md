# INC-202608-DOCKER-03 — Jenkins Attempted to Push an Image That Had Not Been Built

## Incident Metadata

| Field | Details |
| --- | --- |
| Status | Resolved |
| Classification | CI/CD container-build incident |
| Incident window | August 2026; exact date not preserved |
| Environment | Jenkins Shared Libraries, Groovy, Docker, Docker Hub |
| Detection | Jenkins and Docker console output |
| Owner | Ethan Jones |

## Impact Assessment

| Impact area | Details |
| --- | --- |
| Technical impact | Jenkins attempted to push an image tag that had never been built. |
| Operational impact | Image creation, publication, and downstream deployment were blocked. |
| Scope | One Jenkins pipeline, its Shared Library, and one Docker image workflow. |
| Production impact | None — development environment |
| Customer impact | None |
| Data impact | No data loss or corruption identified. |
| Security impact | No credential exposure identified. |

## Symptoms and Evidence

The pipeline returned:

```text
tag does not exist: ejones904/demo-app:jma-19
```

Investigation then exposed several linked defects:

- `builddockerImage()` and `buildDockerImage()` were different case-sensitive methods.
- The method expected to build the image actually ran `docker push`.
- Single-quoted Groovy strings prevented `${IMAGE_NAME}` interpolation.
- `IMAGE_NAME` was referenced before definition.
- After build logic was corrected, Docker reported `open Dockerfile: no such file or directory`.

## Investigation

The exact tag passed to build and push was traced through the Jenkinsfile, `vars/` wrappers, and `Docker.groovy`. Repository searches confirmed that `IMAGE_NAME` was undefined. The error progression showed that Docker access was working and that the remaining faults were pipeline implementation and build-context problems.

## Root Cause

The Shared Library mixed build, login, and push responsibilities and invoked the wrong Docker operation. The pipeline also lacked a consistently defined tag and a root-level Dockerfile.

## Resolution

- Separated `buildDockerImage()`, `dockerLogin()`, and `dockerPush()` responsibilities.
- Defined a traceable tag using `env.BUILD_NUMBER`.
- Passed the exact same fully qualified image name to build and push.
- Used interpolating strings where dynamic values were required.
- Added `./Dockerfile`, matching the `docker build ... .` context.

Working pattern:

```groovy
def imageTag = "jma-${env.BUILD_NUMBER}"
buildImage "ejones904/demo-app:${imageTag}"
dockerLogin()
dockerPush "ejones904/demo-app:${imageTag}"
```

## Validation

Jenkins executed:

```bash
docker build -t ejones904/demo-app:jma-20 .
```

and successfully pushed the same tag to Docker Hub. The pipeline completed end to end.

## Prevention

Use one immutable image reference across build and push, keep pipeline functions single-purpose, and verify required build-context files before execution.

## Skills Demonstrated

Groovy debugging, Jenkins Shared Libraries, Docker build contexts, image tagging, pipeline traceability, layered troubleshooting.

---
