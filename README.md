# KubernetesSolutionsConfig

Kubernetes configuration for the
[KubernetesSolutions](https://github.com/drellxor/KubernetesSolutions) project,
kept apart from the application code so that a config change does not rebuild
images and an image build does not touch the code repository's history.

## Layout

```
project/
  base/                  shared manifests
  overlays/staging/      namespace project-staging, broadcaster logs only
  overlays/prod/         namespace project-prod, database backup included
argocd/
  application.yaml       the two Argo CD Applications
```

## How it updates

The project's pipeline builds and publishes images, then writes the new tags
into the matching overlay here:

- a push to `main` in the code repository updates `project/overlays/staging`
- a tag updates `project/overlays/prod`

Argo CD watches this repository and syncs each overlay into its namespace.

## Applying by hand

```sh
kubectl apply -f argocd/application.yaml
kubectl kustomize project/overlays/staging   # to preview
```
