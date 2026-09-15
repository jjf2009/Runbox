# Module 9: CI/CD Pipeline

## Why this module

Manual `docker build && docker push && kubectl apply` doesn't scale past one person and is the exact toil devops automates away. This module builds the pipeline that tests, lints, security-scans, builds, and (optionally) deploys Runbox automatically on every push — table-stakes for any job-ready devops portfolio project.

## Learning objectives

- GitHub Actions fundamentals: workflows, jobs, steps, matrix builds, caching, secrets
- Pipeline stage design: lint → unit test → build image → scan image → push → (deploy)
- Trunk-based vs branch-based triggers; running expensive integration/e2e tests only where it makes sense
- Image vulnerability scanning (Trivy or Grype) as a required, non-optional pipeline gate
- Managing secrets (registry credentials, kubeconfig) safely in CI

## Build tasks

- [ ] Set up a GitHub Actions workflow: on every PR, run `go vet`/`golangci-lint` and `go test ./...`
- [ ] Add a job that runs your Module 2-6 integration tests (spin up Redis as a service container in the workflow) so sandboxing/queue behavior is verified on every PR, not just locally
- [ ] Add a build job: multi-stage Docker build from Module 7, tagged with the git SHA and (on tags/releases) semver
- [ ] Add an image scan step (Trivy) — fail the pipeline on high/critical CVEs, don't just log them
- [ ] Push the built image to your registry (GHCR) only after tests and scan pass, only on `main`/tags
- [ ] (Stretch) Add a CD job that applies the Kubernetes manifests to a real or local cluster on merge to `main`, using a scoped service account/kubeconfig secret
- [ ] Add branch protection requiring the pipeline to pass before merge
- [ ] Add a status badge to the README

## Definition of done

Opening a PR automatically runs lint + tests + integration tests; merging to `main` builds, scans, and publishes a versioned image with zero manual steps. A PR with a failing test or a critical CVE cannot be merged.

## Resources

- GitHub Actions official docs (Workflow syntax, "Using service containers" page specifically — needed for Redis-in-CI)
- Aqua Security's Trivy GitHub Action docs
- golangci-lint README for Go-specific linting setup

## Time estimate

~1 week.
