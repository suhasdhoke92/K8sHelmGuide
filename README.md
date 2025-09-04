# HelmEssentials

This repository provides a comprehensive guide to Helm, the package manager for Kubernetes (K8s). It covers Helm's core concepts, components, installation, usage, and lifecycle management to help you deploy and manage Kubernetes applications efficiently.

## What is Helm?

Helm simplifies the deployment and management of Kubernetes applications by packaging resources (e.g., Deployments, Services, Persistent Volumes (PV), Persistent Volume Claims (PVC), Secrets, and Backup Jobs) into a single unit called a **Helm Chart**. Instead of manually creating YAML files, Helm uses pre-configured templates and customizable values to streamline operations.

Key Helm operations include:
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

Customize charts by modifying the `values.yaml` file or using the `--set` flag during installation.

## Installation and Configuration

Install Helm using one of the following methods:

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
- **Tiller**: Relied on Tiller, a server-side component that interacted with the Kubernetes API, posing security risks due to broad permissions.
- **No RBAC/CRDs**: Lacked native Role-Based Access Control (RBAC) and Custom Resource Definitions (CRDs).
- **State Tracking**: Manual changes via `kubectl` (e.g., `kubectl set image`) were not tracked, causing inconsistencies.
- **Two-way Merge**: Updates could overwrite manual changes.

### Helm 3
- **No Tiller**: Interacts directly with the Kubernetes API, improving security.
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

## Working with Helm

Use the Helm CLI to manage charts and releases. Key commands include:

- **Get Help**:
  ```bash
  helm --help
  helm rollback --help
  helm repo --help
  helm repo update --help
  ```
- **Search for Charts**:
  Search for charts in ArtifactHub or a specific repository:
  ```bash
  helm search hub wordpress
  helm search repo bitnami/wordpress
  ```
- **Manage Repositories**:
  Add, list, or update repositories:
  ```bash
  helm repo add bitnami https://charts.bitnami.com/bitnami
  helm repo list
  helm repo update
  ```
- **Install and Uninstall**:
  ```bash
  helm install my-release bitnami/wordpress
  helm uninstall my-release
  ```
- **List Releases**:
  ```bash
  helm list
  ```

## Customizing Chart Parameters

Customize chart values in several ways:

1. **Using `--set`**:
   Override specific values during installation:
   ```bash
   helm install --set wordpressBlogName="Helm Tutorials" my-release bitnami/wordpress
   ```

2. **Using a Custom Values File**:
   Create a `custom-values.yaml` file with multiple customizations:
   ```bash
   helm install --values custom-values.yaml my-release bitnami/wordpress
   ```

3. **Pull and Edit Chart**:
   Download and edit the chart’s `values.yaml` before installation:
   ```bash
   helm pull bitnami/wordpress
   helm pull --untar bitnami/wordpress
   ls wordpress/
   # Edit wordpress/values.yaml
   helm install my-release ./wordpress
   ```

## Lifecycle Management with Helm

Helm tracks the lifecycle of releases, including installations, upgrades, and rollbacks:

- **Multiple Installations**:
  Install the same chart with different release names:
  ```bash
  helm install my-site bitnami/wordpress
  helm install my-second-site bitnami/wordpress
  ```

- **Install Specific Version**:
  Specify a chart version:
  ```bash
  helm install nginx-release bitnami/nginx --version 7.1.0
  ```

- **Upgrade a Release**:
  Upgrades create a new revision (e.g., revision 2):
  ```bash
  helm upgrade nginx-release bitnami/nginx
  ```

- **View Release History**:
  Check revision details, chart versions, and app versions:
  ```bash
  helm history nginx-release
  ```

- **Rollback a Release**:
  Revert to a previous revision (creates a new revision, e.g., revision 3):
  ```bash
  helm rollback nginx-release 1
  ```

- **Upgrade Considerations**:
  Upgrading complex applications (e.g., WordPress with a database) may require administrative access to the database or additional parameters:
  ```bash
  helm upgrade wordpress-release bitnami/wordpress
  ```
  **Note**: Helm does not roll back persistent data (e.g., MySQL database content) during rollbacks. It restores pods to their previous state, but data remains unchanged. Use Helm chart hooks for advanced rollback scenarios involving persistent data.

- **List All Releases**:
  ```bash
  helm list
  ```

## Conclusion

Helm streamlines Kubernetes application management with templated, reusable, and version-controlled charts. With Helm 3’s removal of Tiller and support for RBAC and CRDs, it offers a secure and efficient way to deploy complex applications. Leverage charts from repositories like Bitnami, AppsCode, or TrueCharts on [ArtifactHub](https://artifacthub.io/) to simplify your Kubernetes workflows.
