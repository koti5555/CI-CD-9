# MLOps Practical 9 — Automated Docker Build, Test and GHCR Publish

This repository continues the Student Result Prediction application from Practical 8 and automates the build, runtime test, and GHCR publication process with GitHub Actions.

## Workflow

File: `.github/workflows/docker-publish.yml`

Trigger: push to `main` or manual `workflow_dispatch`.

The GitHub-hosted runner performs these steps:

1. Checks out this repository.
2. Installs Python dependencies, trains the same sample model, checks the 0.85 quality gate, and runs the seven ML tests plus four API tests.
3. Builds the Docker image as `student-result-prediction:ci`.
4. Starts a temporary container on port 5000.
5. Retries `GET /health` and verifies the same PASS and FAIL prediction requests used in Practical 7/8.
6. Removes the temporary test container.
7. Logs in to GHCR using the automatically generated `GITHUB_TOKEN`.
8. Pushes the already-tested image as `latest` and `sha-<full-commit-sha>`.

Publishing steps run only after the build and runtime tests succeed. The workflow grants `contents: read` and `packages: write`.

## GHCR image references

- `ghcr.io/koti5555/student-result-prediction-ci-cd-9:latest`
- `ghcr.io/koti5555/student-result-prediction-ci-cd-9:sha-<full-commit-sha>`

## Existing package access

Practical 8 created the GHCR package from the CI-CD-8 repository. If the package is private and the first push from this repository is denied, open the package's **Package settings → Manage Actions access**, add `koti5555/CI-CD-9`, and grant **Write** access. Do not put a personal access token in this repository.

Package: https://github.com/users/koti5555/packages/container/package/student-result-prediction

## API checks

Health: `GET /health`

PASS request:
```json
{"attendance":90,"internal_marks":85,"assignment_marks":88,"previous_score":80}
```

FAIL request:
```json
{"attendance":55,"internal_marks":30,"assignment_marks":40,"previous_score":35}
```

## No local Docker installation required

Docker build, container startup, API checks, authentication, and push run on GitHub-hosted Ubuntu. No Docker Desktop or local PAT is needed.

## Practical 9 observation

Record the Actions run URL, workflow conclusion, commit SHA tag, and package digest after the workflow completes. A later README/application update pushed to `main` should trigger a second run and publish a new SHA tag.
