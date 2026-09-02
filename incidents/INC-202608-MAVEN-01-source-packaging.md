# INC-202608-MAVEN-01 — Maven Could Not Discover or Package the Spring Boot Source

## Incident Metadata

| Field | Details |
| --- | --- |
| Status | Resolved |
| Classification | CI/CD build incident |
| Incident window | August 2026; exact date not preserved |
| Environment | Maven, Java 17, Spring Boot, Jenkins |
| Detection | Jenkins and Maven console output |
| Owner | Ethan Jones |

## Impact Assessment

| Impact area | Details |
| --- | --- |
| Technical impact | Maven could not discover, compile, or package the application source. |
| Operational impact | JAR generation, Docker packaging, and deployment were blocked. |
| Scope | One Java/Spring Boot application and its Jenkins build. |
| Production impact | None — development environment |
| Customer impact | None |
| Data impact | No data loss or corruption identified. |
| Security impact | None identified. |

## Symptoms and Evidence

The Maven stage initially reported a missing project descriptor and later reached packaging but produced:

```text
No sources to compile
Unable to find main class
```

Inspection showed the application at:

```text
src/src/main/java/com/example/Application.java
```

The validation command returned no class before the fix:

```bash
find target/classes -name "Application.class" -print
```

## Investigation

A root-level `pom.xml` was added and the Maven source convention was compared with the actual directory tree. The extra `src` directory prevented Maven from discovering `Application.java`. The Spring Boot plugin's expected main class was checked independently from compilation.

## Root Cause

The Maven project structure did not follow `src/main/java/<package>`, so the source was invisible to Maven. Earlier, the project also lacked the required root `pom.xml`.

## Resolution

- Added a root `pom.xml` with Java 17, dependencies, compiler configuration, and the Spring Boot Maven plugin.
- Moved the application to:

```text
src/main/java/com/example/Application.java
```

- Configured the main class as `com.example.Application`.

## Validation

After correction:

```text
target/classes/com/example/Application.class
target/java-maven-app-1.1.0-SNAPSHOT.jar
```

were generated, proving successful source discovery, compilation, and packaging.

## Prevention

Validate required project files and conventional source paths before pipeline execution. Verify compiled classes separately from packaged JAR output.

## Skills Demonstrated

Maven lifecycle troubleshooting, Java package conventions, Spring Boot packaging, artifact validation, root-cause isolation.

---
