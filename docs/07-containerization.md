# Module 7: Containerization

## Why this module

Runbox needs to ship as a documented, runnable open-source service (per the README) — that means Docker images people can `docker run` without setting up your dev environment. There's an interesting wrinkle here: Runbox itself creates sandboxes using namespaces/cgroups, and running "containers that create containers" (nested isolation) is a real, common devops challenge worth understanding properly rather than papering over with `--privileged`.

## Learning objectives

- Multi-stage Docker builds (compile in a `golang` image, ship a minimal final image)
- Image size/attack-surface hygiene: distroless or `scratch` base images where possible
- What privileges a container needs to create nested namespaces/cgroups (this is where you'll hit and understand `--privileged`, `--cap-add`, and why "just use privileged" is a security smell you should avoid if possible)
- Volume and device considerations for cgroup v2 delegation into a container
- Image versioning/tagging conventions

## Build tasks

- [ ] Write a multi-stage Dockerfile: build stage compiles the Go binary, final stage copies just the binary + minimal rootfs assets (from Module 5) into a slim base image
- [ ] Get Runbox running inside a container that itself spawns sandboxes — identify exactly which capabilities are required (`CAP_SYS_ADMIN` for some mount/namespace ops, cgroup delegation) rather than reaching for `--privileged` by default
- [ ] Document the minimal capability set required, and note explicitly where you had to compromise (if anywhere) and why
- [ ] Add a `.dockerignore`, pin base image versions/digests (not `latest`)
- [ ] Build separate images for the API service and the worker (Module 6) if they're separate binaries — this sets up Module 8's multi-deployment topology
- [ ] Push images to a registry (GitHub Container Registry is a good free option) with semantic version tags
- [ ] Benchmark final image size before/after multi-stage + distroless changes

## Definition of done

`docker run` (with a documented, minimal set of flags — not blanket `--privileged`) starts Runbox and it can execute a sandboxed program from inside the container. Images are versioned and pushed to a registry. README documents exactly what privileges the container needs and why.

## Resources

- Docker's official multi-stage build docs
- `GoogleContainerTools/distroless` README
- Docker docs on `--cap-add`/`--cap-drop` and the full capabilities list (`man capabilities`)
- Kubernetes docs on cgroup driver/delegation (useful preview for Module 8, since this exact "nested isolation" problem resurfaces there)

## Time estimate

~1 week. Expect real friction getting nested sandboxing to work inside a container with minimal privileges — that friction is itself valuable devops experience.
