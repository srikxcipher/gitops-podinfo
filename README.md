# GitOps PodInfo Deployment

![GitOps Workflow](https://img.shields.io/badge/GitOps-Flux-blue)
![Kubernetes](https://img.shields.io/badge/Platform-Kubernetes-blue)
![License](https://img.shields.io/badge/License-MIT-green)

## Overview

This repository demonstrates a GitOps approach to managing Kubernetes applications using Flux CD. It serves as a practical implementation of DevOps best practices, specifically focusing on the continuous deployment of the "podinfo" microservice application through GitOps principles.

## Features

- **GitOps Infrastructure**: Complete declarative infrastructure management via Git
- **Flux CD Integration**: Automated synchronization between Git repository and Kubernetes cluster
- **Progressive Delivery**: Deployment strategies including canary releases and A/B testing
- **Environment Segregation**: Separate configurations for development, staging, and production
- **Kubernetes-Native**: Leverages Kubernetes Custom Resources for deployment management
- **Observability**: Integration with monitoring and logging solutions

## Architecture

The repository follows a structured approach to GitOps deployment:

```
gitops-podinfo/
├── LICENSE                        
├── README.md                     
├── apps                           # Application definitions (Kustomize/Helm overlays)
│   ├── base                       # Base configs shared by all environments
│   │   └── podinfo
│   │       ├── deployment.yaml        # Base deployment manifest
│   │       └── kustomization.yaml     # Kustomize base config
│   ├── team-base                  # Team-specific overlay using Kustomize
│   │   ├── namespace.yaml             # Namespace for team-base
│   │   └── podinfo
│   │       └── kustomization.yaml     # Team-base overlay kustomization
│   ├── team-helm                  # Team-specific overlay using Helm
│   │   ├── namespace.yaml             # Namespace for team-helm
│   │   └── podinfo
│   │       ├── helmrelease.yaml       # HelmRelease resource for PodInfo
│   │       ├── helmrepository.yaml    # Helm chart repo definition
│   │       └── kustomization.yaml     # Kustomize config for Helm overlay
│   └── team-overlay               # Custom overlay for another team
│       ├── namespace.yaml             # Namespace for team-overlay
│       └── podinfo
│           ├── deployment.yaml        # Team-specific deployment patch
│           └── kustomization.yaml     # Team-overlay kustomization
├── clusters                       # Cluster-specific configs
│   └── cluster-podinfo
│       ├── apps-team-base.yaml        # Syncs team-base apps into cluster
│       ├── apps-team-helm.yaml        # Syncs team-helm apps into cluster
│       ├── apps-team-overlay.yaml     # Syncs team-overlay apps into cluster
│       ├── flux-system                # Flux GitOps controllers and settings
│       │   ├── gotk-components.yaml       # Flux core components
│       │   ├── gotk-sync.yaml             # Flux sync settings
│       │   └── kustomization.yaml         # Flux system kustomization
│       └── infrastructure.yaml        # Shared infra for this cluster
└── infrastructure                 # Shared infrastructure definitions
    ├── imagepolicy.yaml               # Image update policy
    ├── imagerepository.yaml           # Image repository source
    └── imageupdateautomation.yaml     # Automate image updates in Git
    
```

## Technologies Used

- [Flux CD](https://fluxcd.io/) - GitOps operator for Kubernetes
- [Kustomize](https://kustomize.io/) - Kubernetes native configuration management
- [podinfo](https://github.com/stefanprodan/podinfo) - Demo microservice application
- [Kubernetes](https://kubernetes.io/) - Container orchestration platform

## Prerequisites

- Kubernetes cluster (v1.20+)
- kubectl configured to communicate with your cluster
- Flux CLI installed
- Git

## Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/srikxcipher/gitops-podinfo.git
cd gitops-podinfo
```

### 2. Bootstrap Flux on your cluster

```bash
flux bootstrap github \
  --owner=srikxcipher \
  --repository=gitops-podinfo \
  --path=clusters/dev \
  --personal
```

### 3. Verify installation

```bash
kubectl get pods -n flux-system
```

### 4. Access the podinfo application

Once deployed, you can access the podinfo service:

```bash
kubectl -n podinfo port-forward svc/podinfo 9898:9898
```

Visit http://localhost:9898 in your browser.

## Workflow

This project implements a full GitOps workflow:

1. **Changes committed to Git**: All infrastructure and application changes are committed to this repository
2. **Flux detects changes**: Flux continuously monitors the repository for changes
3. **Automated reconciliation**: Changes are automatically applied to the cluster
4. **Status reporting**: Deployment status is reflected back to Git

## Testing

The project includes:
- Integration testing for deployments
- Load testing using k6
- Chaos testing to ensure resilience

## Monitoring

Monitoring is set up with:
- Prometheus for metrics collection
- Grafana dashboards for visualization
- Alerting for critical events

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Acknowledgments

- [Flux community](https://fluxcd.io/) for developing the GitOps toolkit
- [Stefan Prodan](https://github.com/stefanprodan) for creating the podinfo demo application
- The Kubernetes community
