# GitOps Configuration Repository

This repository is a scaffold for GitOps-managed Kubernetes configuration.

It is designed to support:

- ArgoCD continuous delivery
- Kustomize-based overlays and environment-specific configuration
- Development, staging, and production environments
- Kubernetes deployments and manifests

## Directory structure

- `argocd/` — ArgoCD application definitions and GitOps configuration.
- `base/` — Common Kubernetes resources shared across environments.
- `overlays/` — Environment-specific overlays for Kustomize.
- `overlays/dev/` — Development environment overlay configuration.
- `overlays/staging/` — Staging environment overlay configuration.
- `overlays/prod/` — Production environment overlay configuration.
- `docs/` — Documentation for repository structure, deployment patterns, and GitOps workflows.

## Next steps

This repository currently contains directory scaffolding and placeholder documentation. The next phase is to add Kustomize manifests, ArgoCD application definitions, and environment-specific configurations.
