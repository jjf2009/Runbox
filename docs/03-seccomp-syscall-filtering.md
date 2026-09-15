# Module 3: Seccomp Syscall Filtering

## Why this module

Namespaces and cgroups control *what a process can see* and *how many resources it can use* — they don't stop it from making dangerous syscalls (e.g. `ptrace` to break out, `reboot`, raw socket creation, mount syscalls). Seccomp-bpf lets you allowlist/denylist syscalls at the kernel level. This is the same mechanism Docker's default seccomp profile and gVisor rely on, and it's the piece that turns Runbox from "isolated" into "actually safe to run untrusted code."

## Learning objectives

- What seccomp-bpf is and how it differs from capabilities and namespaces
- Allowlist vs denylist filtering strategy (allowlist is what production sandboxes use)
- Docker's default seccomp profile (read the actual JSON) as a reference allowlist
- How a seccomp filter attaches to a process before `execve`
- Tradeoffs: a too-strict filter breaks legitimate language runtimes (e.g. Python needs certain syscalls at startup you might not expect)

## Build tasks

- [ ] Read Docker's default seccomp profile (`default.json` from moby/moby) and identify the ~15-20 syscalls most commonly denied (e.g. `ptrace`, `mount`, `reboot`, `kexec_load`, `unshare` itself for nested sandboxes)
- [ ] Pick a Go seccomp library (e.g. `seccomp/libseccomp-golang`) or shell out to a helper using raw BPF
- [ ] Build a syscall allowlist profile for each supported language runtime (Python, Node, C via gcc+run) — start permissive, tighten by running real programs and logging denied syscalls
- [ ] Apply the seccomp filter to the sandboxed process before it execs the target binary/interpreter
- [ ] Add a "trace mode" flag that logs (rather than kills) on a denied syscall, so you can iterate on the profile without constantly breaking test programs
- [ ] Write a test suite of intentionally malicious programs (attempt `ptrace`, attempt raw socket, attempt `mount`) and confirm each is killed with `SIGSYS`
- [ ] Document the allowlist per language in the repo (this becomes real security documentation)

## Definition of done

Your Python, Node, and C sandboxes run real "hello world" / sorting-algorithm style test programs successfully, but a program attempting `ptrace(PTRACE_TRACEME, ...)` or opening a raw socket is killed immediately by seccomp, not by your own code checking for it.

## Resources

- `man seccomp`, `man seccomp_rule_add` (if using libseccomp)
- moby/moby's `profiles/seccomp/default.json` — read this directly, it's the industry reference
- `seccomp/libseccomp-golang` GitHub README and examples
- Julia Evans' seccomp zine/blog posts

## Time estimate

~1 week. Expect to spend real time debugging "why did my Python interpreter just die" — that debugging loop is the actual learning.
