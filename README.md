# 🚀 GitOps Platform Configuration

![Kubernetes](https://img.shields.io/badge/Kubernetes-Platform-326CE5?logo=kubernetes&logoColor=white)
![ArgoCD](https://img.shields.io/badge/ArgoCD-GitOps-EF7B4D?logo=argo&logoColor=white)
![Kustomize](https://img.shields.io/badge/Kustomize-Overlays-5C2D91)
![Kind](https://img.shields.io/badge/Kind-Local%20Cluster-2E3440)
![Security](https://img.shields.io/badge/Security-Trivy%20Evidence-1904DA)
![Environments](https://img.shields.io/badge/Environments-Dev%20%7C%20Staging%20%7C%20Prod-0A7EA4)

## 🚀 Project Overview

This repository is the declarative GitOps configuration source for the Production-Grade GitOps Platform. It defines how the `sample-microservice` is deployed across development, staging, and production environments using Kubernetes, Kustomize, and ArgoCD.

ArgoCD consumes this repository as the desired state for the platform. Every environment is represented as code, versioned in Git, rendered through Kustomize, and reconciled into a local Kind Kubernetes cluster.

This repository demonstrates practical platform engineering capabilities:

* Reusable Kubernetes base manifests
* Environment-specific Kustomize overlays
* ArgoCD Application definitions
* Multi-environment namespace isolation
* Automated reconciliation from Git to Kubernetes
* Security validation evidence stored with the platform configuration

| Capability | Implementation |
| ---------- | -------------- |
| GitOps delivery | ArgoCD Application manifests tracking the `main` branch |
| Kubernetes operations | Namespaced workloads, Services, Deployments, and replica management |
| Multi-environment management | Dev, staging, and production overlays |
| Configuration reuse | Environment-agnostic Kustomize base layer |
| DevSecOps evidence | Trivy filesystem and image scan reports |

## 🎯 Platform Objectives

This repository is designed to model how platform teams manage application delivery configuration across multiple Kubernetes environments.

| Objective | Repository Implementation |
| --------- | ------------------------- |
| Maintain Git as the deployment source of truth | All Kubernetes, Kustomize, and ArgoCD configuration is stored declaratively in this repository. |
| Separate reusable workload configuration from environment-specific settings | `base/` contains shared manifests while `overlays/` applies namespace and replica differences. |
| Support automated Kubernetes reconciliation | ArgoCD Applications track the repository and synchronize overlays into the cluster. |
| Provide clear environment isolation | Dev, staging, and production each deploy into dedicated namespaces. |
| Preserve validation evidence | Trivy filesystem and image scan outputs are retained under `security-reports/`. |

## 🏗️ Repository Architecture

| Directory | Purpose |
| --------- | ------- |
| `base/` | Environment-agnostic Kubernetes resources shared by all deployments. |
| `overlays/` | Kustomize overlays for `dev`, `staging`, and `prod` environments. |
| `argocd/` | ArgoCD Application manifests that map each environment to its overlay. |
| `kind/` | Local Kind cluster configuration used by the platform. |
| `security-reports/` | Trivy security scan evidence captured during validation. |

The repository uses a single authoritative root README to document the deployment model, operational commands, and environment architecture. Kubernetes manifests and ArgoCD Applications remain separate from documentation so the operational configuration stays easy to review.

## ☸️ Kubernetes Deployment Strategy

The Kubernetes deployment model separates shared workload definitions from environment-specific configuration.

| Layer | Responsibility |
| ----- | -------------- |
| Base manifests | Define the reusable `Deployment` and `Service` for `sample-microservice`. |
| Kustomize overlays | Add namespace ownership and environment-specific replica counts. |
| ArgoCD Applications | Continuously reconcile each overlay into the target Kubernetes namespace. |

The base layer is intentionally environment-agnostic. It does not hardcode namespaces or environment-specific values. Each overlay owns its namespace and deployment sizing, which prevents staging and production workloads from inheriting development defaults.

## 🔄 GitOps Workflow

```text
Gitops-Platform-Config
        │
        ▼
GitHub Repository
        │
        ▼
ArgoCD Application Controller
        │
        ▼
Kind Kubernetes Cluster
        │
        ▼
dev / staging / prod namespaces
```

ArgoCD monitors this repository on the `main` branch. When a manifest, Kustomize overlay, or ArgoCD Application definition changes, ArgoCD detects the drift and reconciles Kubernetes state back to the desired state stored in Git.

This model supports a clean operational boundary:

* Git is the source of truth.
* ArgoCD is the reconciliation controller.
* Kustomize renders environment-specific manifests.
* Kubernetes runs the deployed workload in isolated namespaces.

## 📐 GitOps Principles Implemented

| Principle | How It Is Represented |
| --------- | --------------------- |
| Declarative desired state | Kubernetes resources and ArgoCD Applications are stored as YAML manifests. |
| Version-controlled operations | Environment configuration is tracked through Git on the `main` branch. |
| Automated reconciliation | ArgoCD continuously compares live cluster state against repository state. |
| Environment consistency | Each environment consumes the same base manifests through Kustomize overlays. |
| Drift correction | ArgoCD `selfHeal` restores managed resources when live state changes outside Git. |
| Safe cleanup | ArgoCD `prune` removes resources that are no longer declared in Git. |

## 🔁 Deployment Lifecycle

```text
1. Update Kubernetes or Kustomize configuration
2. Commit and push changes to GitHub
3. ArgoCD detects the repository change
4. Kustomize renders the selected environment overlay
5. ArgoCD synchronizes the desired state to Kubernetes
6. Kubernetes updates the workload in the target namespace
7. Operators validate workloads, services, and ArgoCD application health
```

This lifecycle keeps deployment intent auditable in Git while allowing ArgoCD to handle the operational reconciliation loop.

## 🌍 Multi-Environment Architecture

| Environment | Overlay Path | Namespace | Replicas |
| ----------- | ------------ | --------- | -------- |
| DEV | `overlays/dev` | `dev` | 2 |
| STAGING | `overlays/staging` | `staging` | 2 |
| PROD | `overlays/prod` | `prod` | 3 |

Each environment renders the same base workload with an environment-specific namespace and replica count. This keeps deployment behavior consistent while allowing controlled differences between environments.

| Environment | Operational Intent |
| ----------- | ------------------ |
| DEV | Validates application deployment behavior with development isolation. |
| STAGING | Provides a pre-production deployment target using the same base workload. |
| PROD | Runs the production overlay with a higher replica count. |

## 📂 Repository Structure

```text
.
├── README.md
├── argocd
│   ├── prod-microservice.yaml
│   ├── sample-microservice.yaml
│   └── staging-microservice.yaml
├── base
│   ├── deployment.yaml
│   ├── kustomization.yaml
│   └── service.yaml
├── kind
│   └── kind-cluster.yaml
├── overlays
│   ├── dev
│   │   ├── kustomization.yaml
│   │   ├── namespace.yaml
│   │   └── replica-patch.yaml
│   ├── prod
│   │   ├── kustomization.yaml
│   │   ├── namespace.yaml
│   │   └── replica-patch.yaml
│   └── staging
│       ├── kustomization.yaml
│       ├── namespace.yaml
│       └── replica-patch.yaml
└── security-reports
    ├── task-09-filesystem-scan.txt
    └── task-09-image-scan.txt
```

## 📦 Kustomize Configuration

The Kustomize configuration uses a reusable base with targeted overlays.

### Base

`base/` contains:

* `deployment.yaml` - Defines the `sample-microservice` workload.
* `service.yaml` - Exposes the workload through a ClusterIP Service.
* `kustomization.yaml` - References only reusable base resources.

### Overlays

Each overlay contains:

* `kustomization.yaml` - Sets the target namespace and includes the base.
* `namespace.yaml` - Declares the environment namespace.
* `replica-patch.yaml` - Sets the environment replica count.

This structure keeps the base portable and allows environment-specific behavior to be managed cleanly through overlays.

Rendered output can be inspected before deployment with:

```bash
kubectl kustomize overlays/dev
kubectl kustomize overlays/staging
kubectl kustomize overlays/prod
```

## ⚡ Quick Start

Use these commands to inspect, render, and register the GitOps applications from a local workstation with `kubectl` access to the platform cluster.

```bash
git clone https://github.com/Holuphilix/Gitops-Platform-Config.git
cd Gitops-Platform-Config
```

Render an environment before syncing it through ArgoCD:

```bash
kubectl kustomize overlays/dev
```

Register the ArgoCD Applications:

```bash
kubectl apply -f argocd/sample-microservice.yaml
kubectl apply -f argocd/staging-microservice.yaml
kubectl apply -f argocd/prod-microservice.yaml
```

Confirm the Applications exist in ArgoCD:

```bash
kubectl get applications -n argocd
```

## 🚀 ArgoCD Applications

| Application | Manifest | Source Path | Destination Namespace | Sync Policy |
| ----------- | -------- | ----------- | --------------------- | ----------- |
| `sample-microservice` | `argocd/sample-microservice.yaml` | `overlays/dev` | `dev` | Automated prune and self-heal |
| `staging-microservice` | `argocd/staging-microservice.yaml` | `overlays/staging` | `staging` | Automated prune and self-heal |
| `prod-microservice` | `argocd/prod-microservice.yaml` | `overlays/prod` | `prod` | Automated prune and self-heal |

Each Application points to this repository, tracks the `main` branch, and deploys one Kustomize overlay into its matching namespace.

The Applications enable automated drift correction through:

* `prune: true` - Removes resources no longer defined in Git.
* `selfHeal: true` - Restores live resources that drift from the desired state.
* `CreateNamespace=true` - Allows namespace creation during sync when required.

## 🔒 Security Validation

Security validation evidence is stored in `security-reports/`.

| Report | Purpose |
| ------ | ------- |
| `task-09-filesystem-scan.txt` | Trivy filesystem scan evidence for repository-level security validation. |
| `task-09-image-scan.txt` | Trivy container image scan evidence for image-level security validation. |

These reports provide supporting evidence for DevSecOps checks performed during the platform build.

## 👀 Operational Visibility

The repository includes commands and configuration paths that help operators inspect the desired state, rendered state, and live state.

| Visibility Area | Command or Evidence |
| --------------- | ------------------- |
| Rendered manifests | `kubectl kustomize overlays/dev`, `overlays/staging`, `overlays/prod` |
| ArgoCD application state | `kubectl get applications -n argocd` |
| Workload rollout state | `kubectl get deployments -n dev`, `staging`, `prod` |
| Service exposure | `kubectl get services -n dev`, `staging`, `prod` |
| Security evidence | `security-reports/task-09-filesystem-scan.txt`, `task-09-image-scan.txt` |

## ✅ Validation Commands

Render Kustomize overlays locally:

```bash
kubectl kustomize overlays/dev
kubectl kustomize overlays/staging
kubectl kustomize overlays/prod
```

Apply ArgoCD Applications:

```bash
kubectl apply -f argocd/sample-microservice.yaml
kubectl apply -f argocd/staging-microservice.yaml
kubectl apply -f argocd/prod-microservice.yaml
```

Validate deployed workloads:

```bash
kubectl get namespaces
kubectl get deployments -n dev
kubectl get deployments -n staging
kubectl get deployments -n prod
kubectl get services -n dev
kubectl get services -n staging
kubectl get services -n prod
```

Validate ArgoCD Applications:

```bash
kubectl get applications -n argocd
kubectl describe application sample-microservice -n argocd
kubectl describe application staging-microservice -n argocd
kubectl describe application prod-microservice -n argocd
```

## 📊 Deployment Summary

| Environment | Namespace | Application | Service | Replicas |
| ----------- | --------- | ----------- | ------- | -------- |
| Development | `dev` | `sample-microservice` | `sample-microservice` | 2 |
| Staging | `staging` | `sample-microservice` | `sample-microservice` | 2 |
| Production | `prod` | `sample-microservice` | `sample-microservice` | 3 |

The deployed application image is `holuphilix/sample-microservice:latest`, with `imagePullPolicy: Always`.

## 🎯 Key Features

* GitOps-first repository structure
* ArgoCD automated synchronization
* Kustomize-based environment overlays
* Namespace isolation for dev, staging, and production
* Reusable Kubernetes base manifests
* Environment-specific replica management
* Local Kind cluster support
* Security scan evidence retained with the configuration repository

## 🧩 Platform Capabilities Matrix

| Capability | Status | Evidence |
| ---------- | ------ | -------- |
| Reusable base manifests | Implemented | `base/deployment.yaml`, `base/service.yaml` |
| Dev environment overlay | Implemented | `overlays/dev` with namespace `dev` and 2 replicas |
| Staging environment overlay | Implemented | `overlays/staging` with namespace `staging` and 2 replicas |
| Production environment overlay | Implemented | `overlays/prod` with namespace `prod` and 3 replicas |
| ArgoCD application automation | Implemented | `argocd/*.yaml` with automated prune and self-heal |
| Local Kubernetes platform config | Implemented | `kind/kind-cluster.yaml` |
| Security validation evidence | Implemented | `security-reports/` |

## 🧭 Platform Engineering Practices Demonstrated

| Practice | Repository Evidence |
| -------- | ------------------- |
| Declarative delivery | Kubernetes and ArgoCD manifests stored in Git |
| Environment promotion model | Separate overlays for dev, staging, and production |
| Configuration reuse | Shared base manifests consumed by all overlays |
| Operational isolation | Dedicated namespaces for each environment |
| Deployment validation | Local Kustomize render commands and Kubernetes inspection commands |
| Security evidence | Trivy filesystem and image scan reports |

## 📚 Lessons Learned

* Keep the Kustomize base environment-agnostic so it can be reused safely across all overlays.
* Define namespaces in overlays when each environment deploys to a different namespace.
* Store ArgoCD Application manifests alongside the configuration they deploy for clear operational traceability.
* Keep repository documentation centralized when nested README files only repeat root-level information.
* Preserve security reports as evidence artifacts instead of mixing validation output into manifests.

## 🔗 Related Repositories

| Repository | Purpose |
|------------|---------|
| https://github.com/Holuphilix/Production-Grade-GitOps-Platform | Master project documentation, architecture diagrams, screenshots, validation evidence, and implementation roadmap. |
| https://github.com/Holuphilix/Sample-Microservice | Application source code, Docker image build process, CI pipeline, observability endpoints, and security validation. |
| https://github.com/Holuphilix/Gitops-Platform-Config | Declarative Kubernetes manifests, Kustomize overlays, ArgoCD Applications, and GitOps deployment configuration. |

## 🏁 Conclusion

This repository provides a clean, declarative GitOps configuration model for deploying a sample microservice across development, staging, and production environments. It demonstrates how ArgoCD, Kustomize, and Kubernetes work together to deliver repeatable, auditable, multi-environment application deployments from Git.

From a platform engineering perspective, the repository shows the operational foundation required for GitOps-based delivery: reusable base manifests, isolated overlays, automated ArgoCD reconciliation, environment-specific scaling, local Kubernetes platform support, and retained security validation evidence. The result is a concise but practical configuration repository that can be reviewed by engineers, operators, and technical hiring teams.

## 👨‍💻 Author

**Philip Oluwaseyi Oludolamu**

DevOps Engineer | Cloud Engineer | AWS | Kubernetes | Terraform | GitOps | CI/CD

* GitHub: https://github.com/Holuphilix
* Portfolio: https://www.philipoludolamu.com
* LinkedIn: https://www.linkedin.com/in/philip-oludolamu/

Passionate about designing scalable cloud infrastructure, implementing Infrastructure as Code (IaC), automating CI/CD pipelines, and building cloud-native platforms using modern DevOps and Platform Engineering practices.