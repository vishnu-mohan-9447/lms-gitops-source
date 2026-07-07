# cokonet-lms-gitops

Kubernetes manifests only - watched and synced by ArgoCD into the
`cloudcampus-lms-prod` EKS cluster. Nothing else lives in this repo; app
code, Dockerfiles, Terraform, and the Jenkinsfile stay in the `cokonet-lms`
app repo.

## Two structures, same result (for learning)

- `plain-manifests/` - flat YAML files, one per resource. Jenkins updates the
  image tag with `sed` directly against `backend-deployment.yaml` /
  `frontend-deployment.yaml`.
- `kustomize/` - `base/` holds the shared resources, `overlays/prod/` patches
  in the image tag and a prod-specific replica count via
  `kustomize edit set image`. If you later add a staging environment, you'd
  add `overlays/staging/` reusing the same `base/` instead of duplicating
  every YAML file.

Pick whichever one you point your ArgoCD `Application` at
(`argocd/application-plain-manifests.yaml` or
`argocd/application-kustomize.yaml`) - only apply one at a time.

## Before first sync

1. Replace `REPLACE_WITH_RDS_ADDRESS` in the ConfigMap(s) with the
   `db_address` output from `terraform/prod-eks`.
2. Replace the placeholder values in the Secret(s) - see the warning comment
   in `secret.yaml` before doing this.
3. Replace `<your-username>` in the ArgoCD Application manifests with your
   actual GitHub org/user.
