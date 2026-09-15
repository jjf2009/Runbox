# Runbox

Sandboxed code execution engine — run untrusted code safely and get back stdout/stderr/exit code.

Runbox isolates each execution with Linux namespaces, cgroups (CPU/memory/pids limits), seccomp syscall filtering, and no network egress by default. It's the same core problem behind grading platforms, coding-challenge sites, and notebook tools — built here as its own documented, open-source service.

Status: **in development**, built as a learning project — devops/systems skills, module by module. See `docs/` for the full build plan.

## Why

Safe code execution keeps showing up as a feature other products need (grading platforms, coding-challenge sites, notebooks). Runbox pulls that piece out, hardens it, and ships it as a standalone service — talks directly to systems/infra interviewers, and doubles as a real GSoC vehicle.

## Stack

Go engine · Docker · Kubernetes · GitHub Actions · Terraform · Prometheus/Grafana · Redis

## Roadmap

Full module-by-module build plan (each with objectives, tasks, definition of done, resources): **[docs/00-overview.md](docs/00-overview.md)**

| # | Module |
| - | ------ |
| 1 | [Linux Isolation Foundations](docs/01-linux-isolation-foundations.md) |
| 2 | [Core Sandbox Engine](docs/02-core-sandbox-engine.md) |
| 3 | [Seccomp Syscall Filtering](docs/03-seccomp-syscall-filtering.md) |
| 4 | [Network Isolation](docs/04-network-isolation.md) |
| 5 | [Filesystem Isolation](docs/05-filesystem-isolation.md) |
| 6 | [API & Job Queue](docs/06-api-and-job-queue.md) |
| 7 | [Containerization](docs/07-containerization.md) |
| 8 | [Kubernetes Orchestration](docs/08-kubernetes-orchestration.md) |
| 9 | [CI/CD Pipeline](docs/09-cicd-pipeline.md) |
| 10 | [Infrastructure as Code](docs/10-infrastructure-as-code.md) |
| 11 | [Observability](docs/11-observability.md) |
| 12 | [Security Hardening](docs/12-security-hardening.md) |
| 13 | [Scaling & Queueing](docs/13-scaling-and-queueing.md) |
| 14 | [Docs & Open Source Polish](docs/14-docs-and-open-source-polish.md) |

## License

See [LICENSE](LICENSE).
