# Jenkins to GitHub Actions migration report

## Source and archive

| Original Jenkins configuration | Archived copy | GitHub Actions workflow |
| --- | --- | --- |
| `Jenkinsfile` | `.github/ci-archive/Jenkinsfile` | `.github/workflows/android-flavor.yml` |
| `matrixbuilds/Jenkinsfile` | `.github/ci-archive/matrixbuilds-Jenkinsfile` | `.github/workflows/java-matrix.yml` |

The original Jenkinsfiles were removed after archival. No shared-library calls or Jenkins credential bindings were present.

## Conversion summary

- The Android declarative pipeline is an Ubuntu workflow that selects `QA_<flavor>` or `debug`, builds, tests, lints, uploads reports/APKs, and retains the non-debug deployment placeholder.
- The Java declarative matrix is represented by the eight allowed platform/JDK combinations; the excluded macOS/JDK 8 combination is omitted. It performs Maven build, test, package, report/package upload, artifact collection, and master-only Docker image builds.
- Jenkins `publishTestResults`, `publishHTML`, and `archiveArtifacts` are GitHub Actions artifacts. `cleanWs()` is unnecessary because hosted runners are ephemeral.

## Security, prerequisites, and configuration

- Workflows have read-only `contents` permissions and use commit-pinned official `actions/*` actions. The action commit IDs were resolved from official action repositories during migration.
- No secrets or GitHub variables are required by the supplied Jenkinsfiles. The Docker workflow builds images only; publishing would require a registry credential and an explicit authenticated push step, neither of which was present in Jenkins.
- GitHub-hosted runners provide the Android SDK, Docker, and Maven. Android uses Temurin 17; the Java matrix uses Temurin 8, 11, and 17. Verify these versions against the project before enabling production use.

## Validation

`actionlint` was used to validate the generated workflows. Knowledge-base documents were requested from `jenkins-migrations/.github-private` based on the configured remote, but that repository was not accessible to the migration token.
