# Module 8: Kubernetes Orchestration

## Why this module

This is the module that makes Runbox a "real" deployed service and the one most directly tested in devops interviews. You'll deploy the API and worker as separate Kubernetes workloads, manage resource quotas (crucial here, since workers spawn resource-hungry sandboxes), and handle the security implications of running privileged-ish workloads in a shared cluster.

## Learning objectives

- Core objects: `Deployment` (API service), `Job`/worker `Deployment` with pod autoscaling, `Service`, `ConfigMap`/`Secret`
- Resource requests/limits at the pod level, and how they interact with your own internal per-sandbox cgroup limits (two layers of resource control)
- `PodSecurityContext`/`SecurityContext` — capabilities, `allowPrivilegeEscalation`, seccomp profiles at the pod level (relates directly to Module 3/7)
- RBAC basics: a service account with least-privilege permissions for whatever Runbox needs to talk to the k8s API for (if anything)
- Readiness/liveness probes and graceful shutdown (a worker mid-sandbox-execution needs to drain, not be SIGKILLed instantly)

## Build tasks

- [ ] Write Kubernetes manifests (or Helm chart) for: API `Deployment` + `Service`, worker `Deployment`, Redis (or use a managed/StatefulSet Redis)
- [ ] Set CPU/memory `requests` and `limits` on API and worker pods; document the relationship between pod-level limits and the per-sandbox limits enforced inside Runbox itself
- [ ] Configure `SecurityContext` for the worker pods with the minimal capability set identified in Module 7 — avoid `privileged: true` if at all possible, document if you couldn't avoid it
- [ ] Add liveness/readiness probes; implement graceful shutdown in the worker (finish in-flight sandbox executions before exiting on SIGTERM)
- [ ] Set up a local cluster (kind or minikube) for development; document the exact steps to go from `git clone` to a running Runbox on that cluster
- [ ] Create a `ResourceQuota`/`LimitRange` for the namespace so a burst of submissions can't schedule unbounded worker pods and starve the node
- [ ] Add a `HorizontalPodAutoscaler` for the worker `Deployment` keyed on CPU or a custom queue-depth metric (ties into Module 11)
- [ ] Write a runbook doc: how to deploy, how to check logs, how to scale workers manually

## Definition of done

`kubectl apply -f manifests/` (or `helm install`) brings up a working Runbox on a local cluster, handles concurrent submissions across multiple worker pods, and survives a worker pod being deleted mid-job (job gets picked up elsewhere thanks to Module 6's queue design).

## Resources

- Official Kubernetes docs: Deployments, Jobs, SecurityContext, ResourceQuota, HPA (the concept pages, not just API reference)
- kind (`kubernetes-sigs/kind`) or minikube docs for local cluster setup
- Kubernetes Patterns (O'Reilly) if you want a book-length treatment
- Helm docs if you choose to package as a chart instead of raw manifests

## Time estimate

~2 weeks. This module has the most moving parts — don't compress it just to "finish k8s"; the resource-quota and security-context decisions are where the real learning is.
