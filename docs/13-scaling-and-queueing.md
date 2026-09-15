# Module 13: Scaling & Queueing

## Why this module

With observability (Module 11) in place, you can now make Runbox scale intelligently instead of guessing at fixed worker counts. This module is about closing the loop: queue depth and load metrics driving real autoscaling decisions, and the system degrading gracefully instead of falling over under burst load.

## Learning objectives

- Autoscaling on a custom metric (queue depth) rather than just CPU — CPU alone under-reacts to a queue backing up with I/O-bound or just-launched jobs
- Backpressure strategies: reject-early (503 with `Retry-After`) vs unbounded queueing vs priority queueing
- Graceful degradation: what Runbox should do when it's genuinely overloaded, decided deliberately rather than left to chance
- Load testing methodology: how to generate realistic burst/sustained load and read the results correctly

## Build tasks

- [ ] Expose queue depth as a metric consumable by Kubernetes' autoscaler (via `prometheus-adapter` for a custom-metrics HPA, or KEDA for a more direct queue-based scaler)
- [ ] Configure the worker `HorizontalPodAutoscaler` (started in Module 8) to scale on queue depth instead of/alongside CPU
- [ ] Implement backpressure: once the queue exceeds a configured depth, return `503 Retry-After` on new submissions instead of queueing indefinitely
- [ ] (Stretch) Add priority tiers to the queue (e.g. small/fast jobs shouldn't wait behind a backlog of long-running ones)
- [ ] Write a load test script (k6, or a simple Go/bash script) that ramps submissions from baseline to well beyond capacity
- [ ] Run the load test against the deployed cluster, watch the Grafana dashboard (Module 11), and confirm: workers scale up, queue depth stabilizes rather than growing unbounded, and clients get clean 503s instead of hangs once truly overloaded
- [ ] Document the observed scaling behavior (time to scale up, max sustained throughput) in the repo as a benchmark others can reproduce

## Definition of done

Under a load test that exceeds baseline capacity, you can watch (via Grafana) the worker pool scale up in response to queue depth, throughput stabilize at a new higher level, and — if load exceeds even the scaled capacity — clients receive clean, fast 503 responses rather than the system falling over or requests hanging indefinitely.

## Resources

- KEDA docs (`keda.sh`) if you use it for queue-based scaling — generally simpler than wiring `prometheus-adapter` by hand
- Kubernetes HPA docs, "Autoscaling on custom metrics" section
- k6 (Grafana Labs) docs for load test scripting
- Google SRE Book, chapters on handling overload and addressing cascading failures

## Time estimate

~1.5-2 weeks.
