# Module 6: API & Job Queue

## Why this module

Runbox stops being a CLI tool and becomes a service. Real code-execution platforms are async: a submission is queued, executed by a worker, and polled/pushed back to the client — because sandbox execution can take seconds and you need to control concurrency (only so many sandboxes can run at once without starving the host). This module is standard backend/devops engineering: REST API design, queues, and worker pools.

## Learning objectives

- Sync vs async API design, and why "submit then poll" (or webhook/websocket callback) beats "hold the HTTP connection open" for a variable-latency job
- Job queue patterns: at-least-once delivery, idempotency, dead-letter handling for jobs that repeatedly crash workers
- Backpressure: what happens when submissions arrive faster than sandboxes can be created
- API design basics: versioning, request validation, rate limiting hooks (full implementation in Module 12), meaningful error responses

## Build tasks

- [ ] Design the API: `POST /v1/submissions` (language, source, limits) → `{id, status: queued}`; `GET /v1/submissions/{id}` → status + result once done
- [ ] Stand up Redis (or RabbitMQ) locally; push submissions onto a queue instead of executing inline in the HTTP handler
- [ ] Build a worker process (separate Go binary or goroutine pool) that pulls jobs off the queue and calls your Module 2-5 sandbox engine
- [ ] Store submission state and results in a lightweight store (Redis or Postgres) keyed by submission ID
- [ ] Implement a fixed-size worker pool so concurrent sandbox executions are capped (tie this to host CPU/memory budget)
- [ ] Handle worker crash mid-job: job should be requeued or marked failed, never silently lost
- [ ] Add basic request validation (reject oversized source files, unsupported languages, missing fields) with clear 4xx responses
- [ ] Write integration tests: submit a job, poll until complete, assert result matches expected output; submit 100 jobs concurrently and confirm none are dropped

## Definition of done

You can `curl -X POST` a submission, get an ID back immediately, poll and see it transition `queued → running → completed`, and get the sandboxed program's real output. Killing a worker mid-execution doesn't lose the job.

## Resources

- Redis docs on Lists/Streams as a simple queue, or RabbitMQ's "Work Queues" tutorial (official docs, very approachable)
- Google's API design guide (general REST design principles, look up "Google API Improvement Proposals" or similar official style guides)
- Any writeup on the "outbox pattern" / at-least-once delivery if you want to go deeper on reliability

## Time estimate

~1-1.5 weeks.
