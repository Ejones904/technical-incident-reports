# INC-202608-DOCKER-01 — Jenkins Denied Access to the Docker Daemon Socket

## Incident Metadata

| Field | Details |
| --- | --- |
| Status | Resolved |
| Classification | CI/CD infrastructure incident |
| Incident window | August 2026; exact date not preserved |
| Environment | Jenkins in Docker, Linux host, Docker Engine |
| Detection | Jenkins output and Linux permission inspection |
| Owner | Ethan Jones |

## Impact Assessment

| Impact area | Details |
| --- | --- |
| Technical impact | Jenkins could not access the Docker daemon through its mounted Unix socket. |
| Operational impact | Image builds, registry publication, and container deployment were blocked. |
| Scope | One Jenkins container and the host Docker daemon. |
| Production impact | None — development environment |
| Customer impact | None |
| Data impact | No data loss or corruption identified. |
| Security impact | No exposure identified; access was corrected through controlled group permissions. |

## Symptoms and Evidence

Jenkins failed during `docker build`:

```text
ERROR: permission denied while trying to connect to the docker API at unix:///var/run/docker.sock
script returned exit code 1
```

The socket existed and was mounted:

```bash
ls -l /var/run/docker.sock
```

```text
srw-rw---- 1 root docker ... /var/run/docker.sock
```

The Jenkins identity initially showed no Docker group membership:

```text
uid=1000(jenkins) gid=1000(jenkins) groups=1000(jenkins)
```

## Investigation

`systemctl status docker` confirmed that the host Docker daemon was active. This separated host service health from the permission boundary between the containerized Jenkins user and the mounted Unix socket.

## Root Cause

The Jenkins user lacked group permissions matching the Docker socket, even though the socket was correctly mounted.

## Resolution

The existing Jenkins environment was updated so the Jenkins user had the required Docker-related group access, without rebuilding the entire Jenkins container.

## Validation

The Jenkins identity then included Docker groups:

```text
uid=1000(jenkins) gid=1000(jenkins) groups=1000(jenkins),995(docker),112(dockerhost)
```

Subsequent pipeline runs connected to the daemon and advanced to Docker build logic.

## Prevention

Validate socket mount, socket ownership/mode, effective Jenkins UID/GIDs, and host daemon health as separate checks. Treat Docker socket access as privileged and limit it to the required CI identity.

## Skills Demonstrated

Linux permissions, Unix sockets, UID/GID analysis, containerized Jenkins, Docker daemon troubleshooting, least-privilege awareness.

---
