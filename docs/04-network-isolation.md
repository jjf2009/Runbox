# Module 4: Network Isolation (No Egress)

## Why this module

Untrusted code must not be able to phone home, scan your internal network, exfiltrate data, or use your host as a DDoS proxy. "No network egress from the sandbox" is one of Runbox's headline guarantees (per the README) — this module makes that guarantee real and verifiable, not just assumed.

## Learning objectives

- Network namespaces (`CLONE_NEWNET`) create a network stack with zero interfaces by default — that alone blocks all networking
- The difference between "no interfaces" (total isolation) and "interfaces but firewalled" (needed if you ever want controlled egress, e.g. to fetch a package)
- How to verify isolation from the *outside* (don't trust the sandboxed process's own claims)
- veth pairs and bridges, in case a future module needs controlled, rate-limited egress (e.g. hitting an internal package mirror)

## Build tasks

- [ ] Confirm your Module 2 sandbox already creates a new net namespace with no `veth`/bridge attached — verify with `ip netns exec` equivalent or by inspecting `/proc/<pid>/net/dev` showing only `lo`
- [ ] Write a test program that attempts DNS resolution, an outbound TCP connect, and a raw socket — confirm all fail from inside the sandbox
- [ ] Add an external verification test: from the host, run `nsenter --net=/proc/<pid>/ns/net -- ip addr` while a sandbox is running and confirm no routable interface exists
- [ ] (Stretch) Implement an opt-in "controlled egress" mode using a veth pair + `iptables`/`nftables` rules restricting destination to a single allowlisted host:port (simulates letting code fetch one approved package registry) — keep this off by default
- [ ] Add a Runbox config flag `--network=none|restricted` and document exactly what each mode allows
- [ ] Load-test: run 50 concurrent sandboxes and confirm namespace creation/teardown doesn't leak network namespaces (`ip netns list` / `/proc/*/ns/net` inode count should return to baseline)

## Definition of done

A sandboxed process cannot reach any host, on any port, by any protocol, in default mode — proven by an external test, not just "the code returned an error." Namespace cleanup leaves no leaked `netns` entries after repeated runs.

## Resources

- `man network_namespaces`, `man veth`
- Docker networking internals docs (for contrast — Docker's default *does* give containers network access, useful to understand what you're deliberately not doing)
- `nftables` or `iptables` basics if you attempt the stretch goal

## Time estimate

~3-5 days. Core isolation is quick since namespaces do the heavy lifting by default; most of the time goes to writing convincing verification tests.
