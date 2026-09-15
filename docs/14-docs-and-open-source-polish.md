# Module 14: Docs & Open Source Polish

## Why this module

Per the README, Runbox is meant to ship as its own documented open-source service and double as a GSoC vehicle. Undocumented, unpolished projects don't get contributors, don't get stars, and don't read as "production-grade" to an interviewer skimming your GitHub. This module is the finishing pass that makes everything you built in Modules 1-13 legible to someone who isn't you.

## Learning objectives

- What makes open-source documentation actually usable (quickstart that works in under 5 minutes, not just an API reference)
- Writing a CONTRIBUTING guide that lowers the bar for a first-time contributor (relevant directly for GSoC, where mentors/orgs evaluate exactly this)
- Architecture Decision Records (ADRs) as a lightweight way to document *why*, not just *what* — valuable both for contributors and for your own interview prep later
- Versioning and release process (tags, changelogs) for a project other people might depend on

## Build tasks

- [ ] Rewrite the top-level README: what Runbox is, a quickstart (`docker run` or `docker-compose up` to a working local instance in one command), architecture diagram, link into `docs/`
- [ ] Write `CONTRIBUTING.md`: how to set up a dev environment, run tests, coding style, PR expectations
- [ ] Write a handful of ADRs for real decisions you made (e.g. "Why Go over Rust," "Why seccomp allowlist over denylist," "Why Redis over RabbitMQ for the queue") — these double as interview talking points
- [ ] Produce an architecture diagram (API, queue, workers, sandbox internals, k8s topology) and put it in the README/docs
- [ ] Add a `CHANGELOG.md` and start tagging releases with semver (ties back into Module 9's CI image tagging)
- [ ] Write a short SECURITY.md describing the threat model summary from Module 12 and how to report a vulnerability
- [ ] Add example client usage (a small script or curl examples hitting the real API) so someone can try Runbox without reading Go source
- [ ] If pursuing GSoC: identify 1-2 real orgs whose problem space matches Runbox (sandboxed execution, grading platforms, notebook infra) and read their contributor guidelines/past project ideas to see how Runbox's scope maps to what they look for

## Definition of done

A stranger can clone the repo, follow the README, and have a working local Runbox executing sandboxed code within 5 minutes, with no undocumented steps. The docs explain not just how the system works but why it was built the way it was.

## Resources

- GitHub's own "About READMEs" and open-source guide (`opensource.guide`)
- Any well-regarded ADR template (e.g. Michael Nygard's original ADR format) — keep them short, one decision per file
- Keep a Changelog (`keepachangelog.com`) format reference
- GSoC's own "Guide to being a GSoC contributor" (read the official Google Summer of Code documentation directly, since program specifics change year to year)

## Time estimate

~1 week, but treat it as ongoing — docs should be updated alongside code throughout the whole project, not bolted on at the end. This module is really "the final consolidation pass."
