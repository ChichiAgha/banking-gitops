# GitOps Repo Starter (Argo CD + Helm)

This starter wires Helm deployment from a dedicated GitOps repo for dev and prod.

## What this repo contains

- `argocd/dev-application.yaml`: Argo CD Application for dev
- `argocd/prod-application.yaml`: Argo CD Application for prod
- `environments/dev/values.yaml`: environment overrides for dev
- `environments/prod/values.yaml`: environment overrides for prod
- `.github/workflows/promote-image.yaml`: promotion workflow updating immutable image tag

## How it wires to Terraform outputs

Terraform stack in `infrastructure/terraform` provides:
- EKS cluster
- IRSA role for External Secrets

Use Terraform output `external_secrets_irsa_role_arn` in your External Secrets deployment values (separate operator chart repo) to annotate service account:
- `eks.amazonaws.com/role-arn: <external_secrets_irsa_role_arn>`

## Deployment flow

1. App repo builds/pushes immutable image tag (git SHA).
2. GitOps repo updates `environments/dev/values.yaml` tag.
3. Argo CD syncs dev.
4. After validation, promote same tag to `environments/prod/values.yaml`.
5. Argo CD syncs prod.

## Important

- Replace placeholder `repoURL` values in Argo CD Application manifests.
- Keep this repo separate from app source and Terraform for enterprise controls.
