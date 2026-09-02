# INC-20260804-DOCKER-01 — Node.js Container Could Not Connect to MongoDB

## Incident Metadata

| Field | Details |
| --- | --- |
| Status | Resolved |
| Classification | Development application incident |
| Date | August 4, 2026 |
| Environment | Docker Compose, Node.js, MongoDB, Mongo Express |
| Detection | Application container logs |
| Owner | Ethan Jones |

## Impact Assessment

| Impact area | Details |
| --- | --- |
| Technical impact | The Node.js application could not connect to MongoDB. |
| Operational impact | Database-backed application functionality and validation were blocked. |
| Scope | Three Compose services: `my-app`, `mongodb`, and `mongo-express`. |
| Production impact | None — development environment |
| Customer impact | None |
| Data impact | No loss or corruption; named-volume persistence was validated. |
| Security impact | No unauthorized access or credential exposure identified. |

## Symptoms and Evidence

The Node.js application reported a MongoDB connection failure, including `MongoServerSelectionError` / `ECONNREFUSED`, while attempting port `27017`. The failed targets included loopback addresses:

```text
::1:27017
127.0.0.1:27017
```

The Compose environment contained three services:

```text
my-app
mongodb
mongo-express
```

MongoDB exposed `27017:27017`, Mongo Express used the `mongodb` service, and persistent data was stored through:

```text
mongo-data:/data/db
```

## Investigation

Container state and application logs were compared with the Compose service definitions. MongoDB was running, which shifted the investigation from database availability to the application's connection target. Inside `my-app`, `localhost` referred to the application container itself—not the separate MongoDB container.

The Compose network already provided service discovery using the service name `mongodb`.

## Root Cause

The Node.js application used `localhost:27017`. In a multi-container deployment, this directed the client back into the Node.js container, where no MongoDB process was listening.

## Resolution

The MongoDB connection URI was changed to use Docker's internal service discovery:

```text
mongodb://mongodb:27017
```

The Compose/application environment was aligned with the service name, and the application image was rebuilt so the corrected configuration was included. Service dependency configuration was retained to establish startup ordering, while application logs remained the authority for connection readiness.

## Validation

- The rebuilt `my-app` container connected to `mongodb` without `ECONNREFUSED`.
- Mongo Express also connected through the `mongodb` service name.
- The application operated across the Compose network.
- Data persisted through container recreation because `mongo-data` remained mounted at `/data/db`.

## Prevention

- Never use `localhost` for a different Compose service.
- Define connection URIs through explicit environment variables.
- Validate service names with the Compose configuration before rebuilding.
- Test both container-to-container connectivity and data persistence after deployment.

## Skills Demonstrated

Docker networking, Compose service discovery, container logs, Node.js/MongoDB integration, environment configuration, image rebuilding, persistent volumes.

---
