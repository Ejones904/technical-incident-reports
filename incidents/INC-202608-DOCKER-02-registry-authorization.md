# INC-202608-DOCKER-02 — Docker Hub Push Rejected for Unauthorized Repository

## Incident Metadata

| Field | Details |
| --- | --- |
| Status | Resolved |
| Classification | CI/CD registry incident |
| Incident window | August 2026; exact date not preserved |
| Environment | Jenkins, Docker Hub, Jenkins Credentials |
| Detection | Jenkins and Docker registry output |
| Owner | Ethan Jones |

## Impact Assessment

| Impact area | Details |
| --- | --- |
| Technical impact | Docker Hub rejected publication to a repository outside the authenticated namespace. |
| Operational impact | The versioned image could not be stored for downstream deployment. |
| Scope | One Jenkins pipeline and one Docker Hub publication target. |
| Production impact | None — development environment |
| Customer impact | None |
| Data impact | No data loss or corruption identified. |
| Security impact | No secret exposure; authentication used Jenkins-managed credentials. |

## Symptoms and Evidence

The training pipeline attempted to push:

```text
nanatwn/demo-app
```

Docker Hub returned:

```text
push access denied, repository does not exist or may require authorization
server message: insufficient_scope: authorization failed
```

## Investigation

Authentication and repository ownership were evaluated separately. The target belonged to the training/demo account, not the authenticated user's Docker Hub namespace.

## Root Cause

The pipeline used a repository owned by another account. Valid authentication did not grant permission to publish into that namespace.

## Resolution

- Changed the target to `ejones904/demo-app`.
- Stored Docker Hub credentials in Jenkins under `docker-hub-repo`.
- Used Jenkins credential injection and `docker login --password-stdin` rather than hard-coded secrets.

## Validation

The pipeline authenticated and later published versioned images to:

```text
docker.io/ejones904/demo-app
```

## Prevention

Parameterize registry ownership, verify repository existence and account permissions before publishing, and keep credentials outside source control.

## Skills Demonstrated

Container registry authorization, Jenkins Credentials, secure authentication, Docker Hub namespaces, access-scope troubleshooting.

---
