# INC-20260717-DOCKER-01 — Docker Client Could Not Connect and Push to Nexus Hosted Registry

## Incident Metadata

| Field | Details |
| --- | --- |
| Status | Resolved |
| Classification | Development infrastructure incident |
| Date | July 17, 2026 |
| Environment | DigitalOcean Ubuntu server, Nexus Repository Manager, Docker Engine |
| Detection | Docker client output and registry testing |
| Owner | Ethan Jones |

## Impact Assessment

| Impact area | Details |
| --- | --- |
| Technical impact | Docker clients could not use the Nexus-hosted registry on port `8083`. |
| Operational impact | The application image could not be published to the private repository. |
| Scope | One Nexus lab server, one hosted Docker repository, and its Docker client. |
| Production impact | None — development environment |
| Customer impact | None |
| Data impact | No data loss or corruption identified. |
| Security impact | No credential exposure identified; the non-TLS registry was limited to the lab. |

## Evidence Status

The project record confirms port `8083`, firewall and registry connectivity, Docker insecure-registry configuration, authentication, image tagging and pushing, and `curl` API validation. The original error wording was not preserved and is not reconstructed here.

## Symptoms and Evidence

Nexus was running, but the Docker-hosted repository endpoint on port `8083` was not initially usable through the complete client-to-registry path. The investigation covered:

- Nexus hosted Docker repository configuration;
- DigitalOcean/cloud firewall access to `8083`;
- Docker client treatment of a non-TLS lab registry;
- registry authentication;
- correct image naming/tagging; and
- HTTP/API responses from Nexus.

## Investigation

The registry was checked layer by layer:

```text
Docker client
   → Docker daemon registry policy
   → cloud/host firewall
   → DigitalOcean network path
   → Nexus connector :8083
   → hosted repository and credentials
```

Direct `curl` requests were used to separate Nexus/API availability from Docker client behavior. The Docker daemon configuration was reviewed because a lab registry served without TLS is rejected unless explicitly allowed.

## Root Cause

The Docker client, network policy, and Nexus connector were not initially aligned. Port `8083` had to be reachable, and the Docker daemon had to recognize the non-TLS Nexus endpoint as an allowed insecure registry for this lab environment.

## Resolution

- Configured a Nexus hosted Docker repository with its connector on port `8083`.
- Allowed the required port through the cloud/host firewall.
- Added the Nexus host and port to the Docker daemon's `insecure-registries` configuration for the lab.
- Restarted/reloaded Docker so the daemon configuration took effect.
- Authenticated with `docker login`.
- Tagged the application image with the Nexus registry address and pushed `my-app:1.0`.

## Validation

- `curl` returned the expected Nexus repository/API response.
- Docker login completed against the Nexus endpoint.
- The tagged `my-app:1.0` image pushed successfully and was visible in the hosted repository.

## Prevention

- Validate connector port, firewall reachability, daemon trust policy, credentials, and image tag independently.
- Use TLS and a trusted certificate for any production registry; `insecure-registries` is appropriate only for the documented lab context.
- Record the exact successful registry URL and test commands in the runbook.

## Skills Demonstrated

Nexus Repository Manager, Docker registries, Linux daemon configuration, firewalls, authentication, API testing, image tagging and publication.

---
