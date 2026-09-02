# INC-202608-JENKINS-01 — Jenkins Could Not Retrieve Pipeline and Shared Library Files

## Incident Metadata

| Field | Details |
| --- | --- |
| Status | Resolved |
| Classification | CI/CD development incident |
| Incident window | August 2026; exact date not preserved |
| Environment | Jenkins, GitHub, Jenkins Shared Libraries, Groovy |
| Detection | Jenkins console output |
| Owner | Ethan Jones |

## Impact Assessment

| Impact area | Details |
| --- | --- |
| Technical impact | Jenkins could not retrieve or load the required pipeline and Shared Library files. |
| Operational impact | Pipeline initialization and every downstream build stage were blocked. |
| Scope | One Jenkins pipeline and the `Jenkins-shared-library` repository. |
| Production impact | None — development environment |
| Customer impact | None |
| Data impact | No data loss or corruption identified. |
| Security impact | No credential exposure identified. |

## Symptoms and Evidence

The initialization failures changed as Jenkins progressed through configuration:

```text
ERROR: Unable to find Jenkinsfile from git
fatal: couldn't find remote ref refs/heads/master
java.nio.file.NoSuchFileException: .../src/script.groovy
```

Jenkins also reported an annotation-resolution failure until the Shared Library declaration was corrected.

## Investigation

The repository structure was compared with Jenkins SCM and Global Pipeline Library settings. Three independent path assumptions were tested:

- Jenkins expected a root-level `Jenkinsfile`, but the file was `src/Jenkinsfile`.
- The Shared Library configuration requested `master`, but the repository used `main`.
- The pipeline still loaded `src/script.groovy` after the file had moved to the repository root.

The Shared Library annotation was corrected to:

```groovy
@Library('jenkins-shared-library') _
```

## Root Cause

Jenkins configuration and pipeline code referenced stale or incorrect repository locations and branch names.

## Resolution

- Set the Pipeline SCM Script Path to `src/Jenkinsfile`.
- Changed the Shared Library default/reference branch from `master` to `main`.
- Updated the workspace-relative load call to `load "script.groovy"`.
- Configured the library as a trusted Global Pipeline Library.

## Validation

Jenkins reported:

```text
Obtained src/Jenkinsfile from git
Loading library jenkins-shared-library@main
Resolved main as branch main
```

The initialization stage then found and loaded `script.groovy`.

## Prevention

Document the canonical branch and repository layout, validate SCM paths after file moves, and treat Jenkins paths as relative to the checked-out workspace.

## Skills Demonstrated

Jenkins SCM configuration, Shared Libraries, Git branch troubleshooting, workspace-relative path analysis, Groovy pipeline initialization.

---
