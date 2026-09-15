# Module 10: Infrastructure as Code (Terraform)

## Why this module

So far your Kubernetes cluster has probably been local (kind/minikube) or clicked together by hand in a cloud console. Real devops work provisions infrastructure declaratively and reproducibly. This module stands up the cloud infra Runbox actually runs on — via code, in version control, reviewable in a PR — instead of manual console clicks.

## Learning objectives

- Terraform core concepts: providers, resources, state, plan/apply, variables/outputs
- Remote state management (why local `terraform.tfstate` doesn't scale to a team, and how a remote backend with locking avoids concurrent-apply corruption)
- Provisioning a managed Kubernetes cluster (EKS/GKE/DOKS — pick whichever cloud you have free-tier/credits for) via Terraform instead of the console
- Modularizing Terraform (separate modules for network, cluster, registry) instead of one giant file
- The blast radius of `terraform destroy`/`apply` — why plan review matters before every apply

## Build tasks

- [ ] Write Terraform to provision: a VPC/network, a managed Kubernetes cluster, a container registry (if not already using GHCR)
- [ ] Configure a remote backend for state (e.g. an object storage bucket + lock table) — even for a solo project, do this properly since it's the pattern real teams require
- [ ] Split configuration into modules (`modules/network`, `modules/cluster`, `modules/registry`) with clear variables/outputs between them
- [ ] Parameterize environments (`dev`/`prod` via `.tfvars` or workspaces) even if you only ever run `dev`
- [ ] Wire the cluster's kubeconfig output into your Module 8 deployment step (manually first, then via CI in a stretch goal)
- [ ] Run `terraform plan` and review it like a PR diff before every `apply` — document this as your personal workflow rule
- [ ] Add a `terraform destroy` runbook step so you can tear down infra when not actively developing (cost control)
- [ ] (Stretch) Add a CI job that runs `terraform plan` on PRs touching `infra/` and posts the plan as a PR comment

## Definition of done

Your cloud Kubernetes cluster (and its networking/registry) exists entirely as Terraform code checked into the repo. You can `terraform destroy` everything and `terraform apply` it back into existence identically. No cloud resource was created by hand in a console.

## Resources

- HashiCorp's official Terraform docs — start with the "Get Started" tutorial for your chosen cloud provider
- Your cloud provider's Terraform provider docs (AWS/GCP/DigitalOcean) for the exact managed-Kubernetes resource
- "Terraform Up & Running" (Yevgeniy Brikman) if you want a book-length treatment of module structure and remote state

## Time estimate

~1.5 weeks. Watch cloud costs — tear down (`terraform destroy`) when not actively working, and prefer a provider with a free tier (e.g. a small DOKS/GKE cluster) while learning.
