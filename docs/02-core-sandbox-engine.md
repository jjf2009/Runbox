# Module 2: Core Sandbox Engine (Go)

## Why this module

This is where Runbox becomes real software instead of shell scripts. You'll write the Go process that takes a program, launches it inside a namespace+cgroup sandbox, enforces CPU/memory/time limits, and captures its output. This is the heart of the whole project.

## Learning objectives

- Go's `os/exec`, and how to set `SysProcAttr` (`Cloneflags`, `Unshareflags`) to create namespaces from Go without shelling out to `unshare`
- Programmatic cgroup v2 management (creating a cgroup, writing limits, assigning a PID, reading usage stats) — either via raw filesystem writes or a library like `containerd/cgroups`
- Enforcing a **wall-clock timeout** (`context.WithTimeout` + killing the process group) vs a **CPU-time limit** (via `rlimit` or cgroup `cpu.max`)
- Capturing stdout/stderr safely with size limits (a malicious program that prints forever shouldn't OOM your host process)
- Structuring a Go CLI: `runbox run --lang=python --file=solution.py --time-limit=5s --mem-limit=256m`

## Build tasks

- [ ] Scaffold a Go module (`go mod init`), CLI entrypoint (use `flag` or `cobra`)
- [ ] Implement `Sandbox.Run(spec RunSpec) (Result, error)` that: creates a cgroup, sets memory/pid limits, forks the target process into new PID+mount namespaces via `SysProcAttr`, waits for completion or timeout
- [ ] Enforce wall-clock timeout: kill the whole process group (not just the parent) on timeout — a subprocess that spawns children must not survive
- [ ] Enforce memory limit via cgroup `memory.max`; detect and report OOM-kill vs normal exit vs timeout vs non-zero exit as distinct `Result` states
- [ ] Cap captured stdout/stderr to e.g. 64KB, truncate rather than buffer unbounded
- [ ] Return structured JSON result: `{stdout, stderr, exit_code, duration_ms, status: "ok"|"timeout"|"oom"|"error", peak_memory_bytes}`
- [ ] Write unit tests for: normal exit, non-zero exit, timeout kill, OOM kill, infinite-output truncation
- [ ] Clean up cgroups after each run (no leaked cgroup directories)

## Definition of done

`runbox run --file=infinite_loop.py --time-limit=2s` reliably kills the process at ~2s and reports `status: timeout`. `runbox run --file=memory_bomb.py --mem-limit=50m` gets OOM-killed under the limit, not the host. Running 20 sandboxes back-to-back leaves zero leaked cgroups or zombie processes.

## Resources

- Liz Rice, *Containers From Scratch* (Go source code, same talk referenced in Module 1)
- `containerd/cgroups` Go library source (even if you write raw filesystem code first, read this for the real-world pattern)
- Go docs: `os/exec`, `syscall.SysProcAttr`
- Kernel docs: `Documentation/admin-guide/cgroup-v2.rst` (controllers: `memory`, `cpu`, `pids`)

## Time estimate

~1-2 weeks. This is the densest module — don't rush it, everything downstream depends on this engine being correct.
