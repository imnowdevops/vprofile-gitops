# vprofile-gitops

A GitOps-based deployment project for the vProfile application.

## Overview

This project manages the deployment of the vProfile application using GitOps principles with Kubernetes and Helm.

## Prerequisites

- Kubernetes cluster
- Helm 3+
- kubectl configured

## Project Structure

```
vprofile-gitops/
├── helm/        # Helm charts
├── manifests/   # Kubernetes manifests
└── README.md
```

## Deployment

```bash
helm upgrade --install vprofile ./helm/vprofilecharts
```

## Technologies

- Kubernetes
- Helm
- ArgoCD / FluxCD
- Docker
