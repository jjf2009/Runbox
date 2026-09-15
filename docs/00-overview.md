# Runbox — Learning Roadmap

Runbox is a sandboxed code execution engine: submit untrusted code, run it safely in an isolated environment (cgroups, seccomp, no network egress), get back stdout/stderr/exit code. It's the same core problem behind Judge0, Piston, and every "run code in the browser" product.

This project is structured as a set of modules. Each module has a concrete build deliverable and a set of devops/systems concepts it forces you to learn. Build them roughly in order — later modules assume earlier ones exist and work.

## Stack

- **Language:** Go (single static binary, first-class syscall/cgroups libraries, dominant language in the container/k8s ecosystem)
- **Containers:** Docker
- **Orchestration:** Kubernetes
- **CI/CD:** GitHub Actions
- **IaC:** Terraform
- **Observability:** Prometheus + Grafana, structured logging
- **Queue:** Redis (or RabbitMQ) for job dispatch

## Module Map

| # | Module | Core Skill |
|---|--------|-----------|
| 1 | [Linux Isolation Foundations](01-linux-isolation-foundations.md) | namespaces, cgroups, chroot (by hand, no code) |
| 2 | [Core Sandbox Engine](02-core-sandbox-engine.md) | Go process control, cgroups v2 API |
| 3 | [Seccomp Syscall Filtering](03-seccomp-syscall-filtering.md) | seccomp-bpf, syscall allowlisting |
| 4 | [Network Isolation](04-network-isolation.md) | network namespaces, no-egress sandboxes |
| 5 | [Filesystem Isolation](05-filesystem-isolation.md) | pivot_root, overlayfs, read-only rootfs |
| 6 | [API & Job Queue](06-api-and-job-queue.md) | REST API design, async job queues |
| 7 | [Containerization](07-containerization.md) | Dockerfiles, multi-stage builds, image hygiene |
| 8 | [Kubernetes Orchestration](08-kubernetes-orchestration.md) | Deployments, Jobs, resource quotas, RBAC |
| 9 | [CI/CD Pipeline](09-cicd-pipeline.md) | GitHub Actions, test/lint/scan/publish |
| 10 | [Infrastructure as Code](10-infrastructure-as-code.md) | Terraform, reproducible cloud infra |
| 11 | [Observability](11-observability.md) | metrics, dashboards, structured logs, tracing |
| 12 | [Security Hardening](12-security-hardening.md) | least privilege, auth, image scanning, rate limits |
| 13 | [Scaling & Queueing](13-scaling-and-queueing.md) | worker pools, autoscaling, backpressure |
| 14 | [Docs & Open Source Polish](14-docs-and-open-source-polish.md) | README, CONTRIBUTING, docs site, GSoC readiness |

## How to use these docs

Each module file has:
- **Why this module** — how it maps to real devops/infra job skills
- **Learning objectives** — concepts you must understand, not just copy
- **Build tasks** — a checklist to implement in Runbox
- **Definition of done** — how you know the module is actually finished
- **Resources** — where to learn the underlying concept
- **Time estimate** — sized for ~1 week part-time

Don't skip ahead to Kubernetes/Terraform before the engine actually works locally — the sandboxing internals (modules 1-5) are the part that makes this project distinctive on a resume/interview versus "yet another CRUD app in a Docker container."

## Milestones

- **M1 (end of module 5):** `runbox run <file>` executes untrusted code locally with real isolation (cgroup limits, syscall filtering, no network, restricted filesystem). This is the systems-engineering core.
- **M2 (end of module 8):** Runbox is a deployed service — HTTP API, containerized, running on Kubernetes, handling concurrent job submissions.
- **M3 (end of module 11):** Runbox has a full devops loop — CI/CD deploys it, Terraform provisions its infra, Prometheus/Grafana show you what it's doing in production.
- **M4 (end of module 14):** Runbox is a polished, documented, open-source project — portfolio- and GSoC-ready.
