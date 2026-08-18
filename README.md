# otel-demo-gitops-local

GitOps desired state for the OpenTelemetry Demo running on the local kind
cluster. This repository is the local counterpart to `otel-demo-gitops`, which
remains dedicated to AWS.

## Repository structure

```text
otel-demo-gitops-local/
└── applications/
    └── otel-demo/
        ├── values.yaml
        └── manifests/
            ├── gateway.yaml
            └── httproute.yaml
```

The local Terraform source repository, `otel-demo-local`, installs Argo CD and
registers an Application that watches `applications/otel-demo` on `main`.
Argo CD combines the pinned upstream OpenTelemetry Demo Helm chart with this
repository's values and local Gateway API resources.

## Responsibilities

This repository owns:

- local Helm value overrides;
- the local `Gateway` and `HTTPRoute`;
- immutable local Recommendation image selection.

It does not own the kind cluster, Argo CD installation, NGINX Gateway Fabric,
application source, or AWS desired state.

## Delivery flow

Pushes to the `local` branch of `otel-demo-apps` publish an arm64 Recommendation
image to GHCR and update `applications/otel-demo/values.yaml` here. Local Argo
CD then detects the commit and reconciles the kind cluster.

The GitHub repository must be public for anonymous Argo CD access, or private
repository credentials must be configured in the local Argo CD installation.
