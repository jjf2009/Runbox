# Module 1: Linux Isolation Foundations

## Why this module

Every container technology (Docker, containerd, gVisor, Kubernetes pods) is built from the same handful of Linux kernel primitives: namespaces, cgroups, and chroot/pivot_root. Before writing any Go code, you should be able to build a crude "container" by hand using nothing but shell commands and `unshare`/`nsenter`. This is the difference between "I use Docker" and "I understand what Docker actually does" — the latter is what systems/infra interviewers probe for.

No coding in this module — it's pure hands-on Linux exploration.

## Learning objectives

- What a **namespace** is (PID, mount, network, UTS, IPC, user) and what it isolates
- What a **cgroup** (v2) is and what it limits (CPU, memory, pids, IO)
- The difference between **isolation** (namespaces) and **resource control** (cgroups)
- What `chroot` does and why it's not a security boundary on its own
- How Docker composes these primitives (you'll confirm this by inspecting a running container)

## Build tasks

- [ ] Use `unshare --pid --fork --mount-proc` to create a process with its own PID namespace. Run `ps aux` inside it and observe your new process is PID 1.
- [ ] Use `unshare --net` to create a network namespace with no interfaces. Confirm `ping 8.8.8.8` fails inside it.
- [ ] Create a cgroup v2 manually under `/sys/fs/cgroup/` (e.g. `runbox-test`), set `memory.max` and `pids.max`, add a shell's PID to `cgroup.procs`, and prove a memory-bomb (`:(){ :|:& };:` equivalent in a safe form, or a script that allocates memory in a loop) gets OOM-killed at your limit instead of taking down the host.
- [ ] Build a minimal chroot jail: create a directory with just enough binaries/libs (use `ldd` to find dependencies) to run `/bin/sh` inside `chroot`. Notice it still shares the host's network and process view.
- [ ] Combine `unshare` (PID + mount + net) + chroot manually into one shell script that launches an isolated shell. This script is effectively "Docker from scratch" in ~30 lines of bash.
- [ ] Start a real Docker container (`docker run -it alpine sh`) and, from the host, inspect `/proc/<pid>/ns/*` for that container's process to see the actual namespaces Docker created. Cross-reference with what you built by hand.

## Definition of done

You can explain, without notes, what happens at the kernel level when someone runs `docker run`. You have a bash script that manually constructs a namespace+cgroup+chroot sandbox and can run a program inside it.

## Resources

- `man unshare`, `man cgroups`, `man namespaces`, `man chroot`
- Julia Evans — "Zines" on containers (free, excellent, short)
- Liz Rice, *Containers From Scratch* talk (search the exact title on YouTube — live-codes a container in Go, good preview of Module 2)
- Kernel docs: `Documentation/admin-guide/cgroup-v2.rst`

## Time estimate

~1 week, a few hours of hands-on terminal work. This module is conceptually small but the hands-on time matters — don't just read about it.
