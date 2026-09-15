# Module 5: Filesystem Isolation

## Why this module

An untrusted program should see only the files it needs (its own source, a language runtime, a scratch dir) and nothing of the host filesystem — no `/etc/passwd`, no other users' submissions, no ability to write outside its sandbox. `chroot` alone is not sufficient (it's escapable); real container runtimes use `pivot_root` plus a purpose-built root filesystem, often with `overlayfs` for cheap, disposable per-run filesystems.

This module completes the "systems core" of Runbox — after this, modules 1-5 together give you a genuinely isolated execution environment, matching the README's promise.

## Learning objectives

- Why `chroot` is escapable (a process with sufficient privilege can break out) and why `pivot_root` inside a mount namespace is the real primitive containers use
- **overlayfs**: lowerdir (read-only base image), upperdir (writable layer), merged view — this is literally how Docker image layers work
- Building a minimal root filesystem per language (a "rootfs") containing just the interpreter/compiler and its shared library dependencies
- Read-only mounts for everything except a small writable `/tmp`-equivalent scratch space
- Per-run disposability: each sandbox run gets a fresh overlay, discarded after

## Build tasks

- [ ] Build a minimal rootfs per supported language (Python, Node, C toolchain) — either hand-built with `ldd`-resolved dependencies, or extracted from a minimal Docker image (`docker export`) as a starting point
- [ ] Implement `pivot_root` in the sandbox's mount namespace setup (Go: `syscall.Mount` + `syscall.PivotRoot`) so the sandboxed process's `/` is the minimal rootfs, not the host's
- [ ] Mount the language rootfs read-only; mount a small tmpfs (e.g. 16MB) at `/tmp` as the only writable location
- [ ] Set up overlayfs so each run gets a fresh writable layer on top of a shared read-only base image — discard the upperdir after each run instead of rebuilding the whole rootfs from scratch each time
- [ ] Verify escape resistance: from inside the sandbox, attempt to read `/etc/shadow`, list `/proc/1`, or write outside `/tmp` — confirm all fail
- [ ] Benchmark: measure sandbox startup time with and without overlayfs reuse; this matters a lot once you're handling concurrent requests (Module 6+)
- [ ] Clean up: ensure overlay mounts and scratch dirs are removed after each run (no disk leak from repeated executions)

## Definition of done

A sandboxed process's filesystem view is a minimal, read-only language environment plus a tiny writable scratch dir — verified by trying (and failing) to access host files from inside. Running hundreds of sandboxes doesn't leak mounts or disk space.

## Resources

- `man pivot_root`, `man overlayfs` (or kernel docs `Documentation/filesystems/overlayfs.rst`)
- runc source code (`libcontainer/rootfs_linux.go`) — the reference implementation of exactly this pattern, worth reading even if you don't copy it directly
- Julia Evans, "chroot is not enough" style write-ups (search for the general concept — many good short posts exist)

## Time estimate

~1-1.5 weeks. `pivot_root` and overlayfs debugging (mount ordering, propagation flags) is fiddly — budget real time for trial and error.
