# INC-202608-GIT-01 — Jenkins Unable to Push Automated Version Commit

## Incident Metadata

| Field | Details |
| --- | --- |
| Status | Resolved |
| Classification | CI/CD source-control incident |
| Incident window | August 2026; exact date not preserved |
| Environment | Jenkins SCM, Git, GitHub, Maven |
| Detection | Jenkins and Git console output |
| Owner | Ethan Jones |

## Executive Summary

Jenkins incremented the Maven version in `pom.xml` but could not push the resulting commit to GitHub. The command `git push origin main` failed because Jenkins SCM had checked out a specific commit in detached HEAD state and no local `main` ref existed.

The push was changed to `git push origin HEAD:main`, allowing Jenkins to push its current commit explicitly to the remote `main` branch. Because Jenkins now wrote to the repository whose webhook triggered the pipeline, SCM Skip and a `[ci skip]` commit marker were added to prevent recursive builds.

## Impact Assessment

| Impact area | Details |
| --- | --- |
| Technical impact | Jenkins could update `pom.xml` locally but could not persist the commit to GitHub. |
| Operational impact | Automated version persistence and the complete CI/CD workflow could not finish. |
| Scope | One Jenkins pipeline, its workspace, and the `Jenkins-shared-library` repository. |
| Production impact | None — development environment |
| Customer impact | None |
| Data impact | No loss or corruption; the unpushed change remained in the temporary workspace. |
| Security impact | No credential exposure; authentication used Jenkins-managed credentials. |

## Detection

```bash
git push origin main
```

returned:

```text
error: src refspec main does not match any
error: failed to push some refs
```

## Investigation

The investigation separated three concerns:

1. **Checkout state:** Jenkins had `HEAD` at a commit but no local `main` branch.
2. **Commit identity:** `user.name` and `user.email` identified the automated author.
3. **Authentication:** the `Jenkins-Github` PAT credential authorized the HTTPS push.

The remote destination `origin/main` was valid; the assumed local source ref `main` was missing.

## Root Cause

The push command assumed a normal local branch checkout. Jenkins SCM was operating from detached HEAD, so `main` did not exist as a local source ref.

## Resolution

The pipeline configured its commit identity:

```bash
git config --global user.name "Jenkins CI"
git config --global user.email "jenkins@local"
```

It then pushed the current checked-out commit explicitly:

```bash
git push origin HEAD:main
```

Automated commits used:

```text
ci: version bump [ci skip]
```

The first pipeline stage applied SCM Skip:

```groovy
scmSkip(
    skipPattern: '.*\\[ci skip\\].*',
    deleteBuild: false
)
```

## Validation

- The version-bump commit appeared on GitHub as authored by `Jenkins CI`.
- The updated `pom.xml` persisted outside the Jenkins workspace.
- Normal developer commits triggered the complete pipeline.
- Jenkins-generated commits reached Jenkins but were stopped by SCM Skip.
- Recursive version bumps did not occur.

## Corrective and Preventive Actions

| Action | Type | Status |
| --- | --- | --- |
| Push `HEAD:main` instead of assuming a local branch | Corrective | Completed |
| Configure an automated commit identity | Corrective | Completed |
| Use Jenkins-managed PAT credentials | Security | Completed |
| Add `[ci skip]` to automated commits | Preventive | Completed |
| Place SCM Skip at the start of the pipeline | Preventive | Completed |
| Limit PAT access to required repository operations | Security | Recommended |

## Lessons Learned

- CI checkouts should not be assumed to create local branch refs.
- Git commit identity and GitHub authentication solve different problems.
- A refspec explicitly maps a local source to a remote destination.
- Automation that writes to its own trigger repository requires a loop-breaking control.

## Skills Demonstrated

Git refspecs, detached HEAD troubleshooting, Jenkins SCM, automated commits, GitHub PAT authentication, webhooks, SCM Skip, and secure CI/CD design.

## Final Status

**Resolved and validated.** Jenkins persisted automated version changes to GitHub without causing recursive pipeline execution.
