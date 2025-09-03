# Helm Overview

This document provides an overview of Helm, a package manager for Kubernetes, and its key concepts, components, and usage.

## What is Helm?

Helm is a package manager for Kubernetes (K8s) designed to simplify the deployment and management of applications. It packages Kubernetes resources (e.g., Deployments, Services, Persistent Volumes (PV), Persistent Volume Claims (PVC), Secrets, and Backup Jobs) into a single unit called a **Helm Chart**. Instead of manually creating YAML files for each resource, Helm allows you to manage complex applications using pre-configured templates and customizable values.

Helm supports the following operations:
- **Install**: Deploy a chart with a specified release name.
  ```bash
  helm install my-site bitnami/wordpress
  ```
- **Upgrade**: Update an existing release with new configurations or chart versions.
  ```bash
  helm upgrade my-site bitnami/wordpress
  ```
- **Rollback**: Revert a release to a previous revision.
  ```bash
  helm rollback my-site <revision-number>
  ```
- **Uninstall**: Remove a release from the cluster.
  ```bash
  helm uninstall my-site
  ```

Customize chart configurations by modifying the `values.yaml` file or using the `--set` flag during installation.

## Installation and Configuration

To install Helm, use one of the following methods:

1. **Using Snap**:
   ```bash
   sudo snap install helm --classic
   ```

2. **Using a Package Manager** (Debian/Ubuntu):
   ```bash
   curl https://baltocdn.com/helm/signing.asc | sudo apt-key add -
   sudo apt-get update
   sudo apt-get install helm
   ```

3. **Manual Installation**:
   - Download the Helm binary from the [official Helm releases page](https://github.com/helm/helm/releases).
   - Extract and move the binary to `/usr/local/bin/helm`.

Add popular chart repositories:
```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
```

## Helm 2 vs Helm 3

### Helm 2
- **Tiller**: Used Tiller, a server-side component that interacted with the Kubernetes API, posing security risks due to broad permissions.
- **No RBAC/CRDs**: Lacked native Role-Based Access Control (RBAC) and Custom Resource Definitions (CRDs).
- **State Tracking**: Manual changes via `kubectl` (e.g., `kubectl set image`) were not tracked, causing inconsistencies.
- **Two-way Merge**: Updates could overwrite manual changes.

### Helm 3
- **No Tiller**: Directly interacts with the Kubernetes API, improving security.
- **RBAC and CRDs**: Supports RBAC and CRDs for better access control and extensibility.
- **Three-way Strategic Merge Patch**: Preserves manual changes during installs, upgrades, and rollbacks.
- **Improved Rollback**: Tracks revisions accurately, allowing rollbacks to specific versions.

## Helm Components

1. **Helm CLI**: The command-line tool for interacting with Helm and Kubernetes.
2. **Helm Charts**: Collections of files defining a Kubernetes application, including templates and configuration values.
3. **Releases**: A chart installation creates a release with a unique revision number. Upgrades create new revisions for rollback purposes.
4. **Repositories**: Charts are hosted in repositories like:
   - **Bitnami**: Production-ready charts.
   - **AppsCode**: Kubernetes operators.
   - **TrueCharts**: Community-driven charts for TrueNAS.
   - **Community Operators**: Available on [ArtifactHub](https://artifacthub.io/).
   Example:
   ```bash
   helm install my-release bitnami/wordpress
   ```

## Helm Chart Structure

A Helm chart includes:
- **Chart.yaml**: Metadata about the chart, including:
  - `apiVersion`: `v2` (Helm 3) or `v1` (Helm 2).
  - `name`: Chart name.
  - `version`: Chart version.
  - `appVersion`: Application version.
  - `type`: `application` or `library` (for reusable dependencies).
- **values.yaml**: Default configuration values, e.g.:
  ```yaml
  replicaCount: 1
  image:
    repository: wordpress
    tag: latest
  ```
  Referenced in templates like `{{ .Values.replicaCount }}`.
- **templates/**: Kubernetes resource templates (e.g., `deployment.yaml`, `service.yaml`).
- **charts/**: Dependency charts.
- **LICENSE**: Licensing information.
- **README.md**: Chart usage and configuration details.

### Templating Example
In `deployment.yaml`:
```yaml
replicas: {{ .Values.replicaCount }}
image: {{ .Values.image.repository }}:{{ .Values.image.tag }}
```
Override values during installation:
```bash
helm install my-release bitnami/wordpress --set replicaCount=3
```

## Common Commands
- Install a chart:
  ```bash
  helm install my-release bitnami/wordpress
  ```
- Upgrade a release:
  ```bash
  helm upgrade my-release bitnami/wordpress
  ```
- Rollback to a revision:
  ```bash
  helm rollback my-release 1
  ```
- List releases:
  ```bash
  helm list
  ```
- Uninstall a release:
  ```bash
  helm uninstall my-release
  ```
- Search for charts:
  ```bash
  helm search hub wordpress
  ```

## Conclusion
Helm streamlines Kubernetes application management with templated, reusable, and version-controlled charts. Helm 3's removal of Tiller and support for RBAC and CRDs make it a secureЛА. Using charts from repositories like Bitnami or TrueCharts, you can deploy complex applications efficiently.# K8sHelmGuide
for learning basics of k8s helm 
