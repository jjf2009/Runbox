# Module 12: Security Hardening

## Why this module

Runbox's entire premise is running untrusted, potentially hostile code — sandboxing internals (Modules 1-5) are half the security story, but the service *around* the sandbox (API auth, rate limiting, dependency hygiene, secrets) is the other half, and it's the half most portfolio projects skip. This module is what lets you credibly say "I built a secure system," not just "I built a system with a sandbox in it."

## Learning objectives

- Defense in depth: no single layer (seccomp, cgroups, network isolation, API auth) should be your only line of defense
- API authentication/authorization patterns (API keys vs OAuth/JWT — pick the one proportional to Runbox's actual use case) and rate limiting to prevent abuse/resource exhaustion at the API layer, not just inside the sandbox
- Secrets management: never in git, never in plain env vars in a manifest — use k8s `Secret` at minimum, a real secrets manager (Vault, cloud KMS) ideally
- Dependency and image supply-chain hygiene: `go mod` vulnerability scanning (`govulncheck`), base image CVE scanning (already started in Module 9, extend it)
- Threat modeling your own system: write down what you're defending against and what's explicitly out of scope

## Build tasks

- [ ] Add API key or JWT auth to the Runbox API; reject unauthenticated submission requests
- [ ] Add rate limiting per API key/client (token bucket or similar) so one client can't flood the queue and starve others
- [ ] Move all secrets (registry creds, Redis password, API signing keys) out of manifests/env files and into k8s `Secret` objects (or a real secrets manager if you want to go further) — audit the repo history to confirm none were ever committed in plaintext
- [ ] Run `govulncheck` in CI (extends Module 9) and fix or explicitly accept-and-document any findings
- [ ] Write a short threat model doc: what Runbox defends against (host compromise via sandboxed code, resource exhaustion, network exfiltration) and what it explicitly does not defend against (e.g. side-channel attacks, kernel 0-days) — be honest about limits
- [ ] Do a manual "red team" pass: try to break your own sandbox (attempt a known container-escape technique appropriate for learning purposes, e.g. a benign fork-bomb, a symlink-based path traversal attempt, a known CVE'd escape pattern in a *contained* test env) and document what happened
- [ ] Add input size limits and validation on submitted source code (already started in Module 6) with security specifically in mind, not just correctness

## Definition of done

Unauthenticated or rate-limit-violating requests are rejected. No secret exists in plaintext anywhere in git history or manifests. CI fails on known-vulnerable dependencies. You have a written threat model and can explain, with specifics, both what Runbox protects against and what it doesn't.

## Resources

- OWASP API Security Top 10 (official OWASP project docs)
- `golang.org/x/vuln/cmd/govulncheck` documentation
- Kubernetes docs on Secrets management and (if you go further) integrating an external secrets manager
- NCC Group / Google Project Zero write-ups on real container escapes (read for understanding, purely educational — do not attempt against systems you don't own)

## Time estimate

~1.5 weeks.
