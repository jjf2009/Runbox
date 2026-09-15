# Module 11: Observability

## Why this module

A deployed service you can't see into is a liability. This module gives Runbox real production visibility: metrics (what's happening in aggregate), logs (what happened to this specific request), and optionally traces (how a request moved through the system). This is a core devops/SRE skill and directly informs the autoscaling work in Module 8/13.

## Learning objectives

- The three pillars: metrics, logs, traces — what each is good for and what it isn't
- Prometheus's pull model, metric types (counter, gauge, histogram), and label cardinality pitfalls
- Writing useful application metrics, not just "requests total" — e.g. sandbox execution duration histogram, queue depth gauge, OOM-kill counter by language
- Structured (JSON) logging with correlation IDs so one submission's log lines can be traced end-to-end across API and worker
- Building a Grafana dashboard that answers real operational questions, not just displaying every metric you can think of

## Build tasks

- [ ] Instrument the Go services with the Prometheus client library: counters for submissions by status (`completed`, `timeout`, `oom`, `error`), a histogram for sandbox execution duration, a gauge for current queue depth and active worker count
- [ ] Expose `/metrics` on both API and worker; deploy `kube-prometheus-stack` (or standalone Prometheus) on your cluster to scrape them
- [ ] Convert all logging to structured JSON with a request/submission correlation ID threaded from API through to worker
- [ ] Ship logs somewhere queryable (Loki is a natural pairing with Prometheus/Grafana; a hosted log service also works)
- [ ] Build a Grafana dashboard answering: current queue depth, p50/p95/p99 sandbox execution time, error/timeout/OOM rate by language, worker pool utilization
- [ ] Set up at least 2 alert rules (e.g. queue depth sustained above N for 5 minutes; error rate above X%) via Alertmanager or Grafana alerting
- [ ] (Stretch) Add basic distributed tracing (OpenTelemetry) for a submission's path through API → queue → worker

## Definition of done

You can open Grafana during a load test (reuse your Module 6 concurrent-submission test) and watch queue depth rise and fall, see the execution-duration histogram populate, and find a specific failed submission's full log trail by its correlation ID.

## Resources

- Prometheus official docs, especially "Metric and label naming" best practices page
- Grafana docs + `kube-prometheus-stack` Helm chart README
- Grafana Loki docs if you add log aggregation
- Google's SRE Book, chapter on monitoring distributed systems (free online) for the conceptual grounding on what to alert on

## Time estimate

~1.5 weeks.
