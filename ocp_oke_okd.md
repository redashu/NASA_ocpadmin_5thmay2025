# OpenShift Kubernetes Engine (OKE), OpenShift Container Platform (OCP), and OKD

## 1. Overview

| Feature       | OKD (Origin Community Distribution) | OpenShift Kubernetes Engine (OKE) | OpenShift Container Platform (OCP) |
|---------------|-------------------------------------|------------------------------------|-------------------------------------|
| **Type**      | Community-driven upstream project   | Minimal enterprise Kubernetes      | Full enterprise PaaS + Kubernetes  |
| **License**   | Open source (Apache 2.0)           | Commercial (Red Hat subscription) | Commercial (Red Hat subscription)  |
| **Support**   | Community-only                     | Red Hat support                   | Red Hat 24/7 enterprise support     |
| **Target Users** | Developers, hobbyists, labs      | Teams needing lightweight Kubernetes | Enterprises needing full PaaS     |

## 2. Key Differences

### A. Features & Components

| Feature            | OKD                              | OKE                              | OCP                              |
|--------------------|----------------------------------|----------------------------------|----------------------------------|
| **Kubernetes Core** | ✅ Latest upstream features      | ✅ Enterprise-hardened Kubernetes | ✅ OCP’s Kubernetes + extensions |
| **Web Console**     | Basic (developer/admin views)   | Admin-only console               | Full developer/admin UI          |
| **CI/CD (Tekton/Jenkins)** | ✅ Community versions      | ❌ Not included                   | ✅ Integrated & supported         |
| **Service Mesh (Istio)** | ❌ Not included             | ❌ Not included                   | ✅ Red Hat Service Mesh (Maistra) |
| **Developer Tools** | Limited (e.g., Source-to-Image) | ❌ Disabled                       | ✅ Pipelines, Dev Spaces, odo CLI |
| **Storage/Networking** | Community plugins           | Same as OCP (CSI, OVN-Kubernetes) | Certified plugins (Ceph, F5, etc.) |
| **Security**        | Basic RBAC/SCC                 | Same as OCP (FIPS, SELinux)      | Advanced (Quay, Clair scanning)  |

### B. Installation & Requirements

- **OKD**:
    - Runs on Fedora CoreOS (FCOS) or RHEL/CentOS 12.
    - Manual or Ansible-based install (no enterprise installer).

- **OKE/OCP**:
    - Requires RHEL CoreOS (control plane) + RHEL workers.
    - Uses `openshift-install` (same binary for both).

### C. Support & Lifecycle

- **OKD**:
    - No SLA; frequent updates (aligned with upstream Kubernetes).

- **OKE/OCP**:
    - **OKE**: 3-year lifecycle (security patches only).
    - **OCP**: 9-year lifecycle with minor version upgrades.

## 3. When to Use Which?

- **Choose OKD if**:
    - You need bleeding-edge features (upstream Kubernetes).
    - You’re experimenting or self-supporting (no Red Hat support).

- **Choose OKE if**:
    - You want enterprise Kubernetes without developer tools (e.g., external CI/CD).
    - Cost optimization is critical (cheaper than OCP).

- **Choose OCP if**:
    - You need full PaaS (built-in CI/CD, Service Mesh, Quay).
    - Enterprise support and long-term stability are required.

## 4. Summary Table

| Aspect       | OKD           | OKE                 | OCP                     |
|--------------|---------------|---------------------|-------------------------|
| **Kubernetes** | Upstream      | Enterprise-hardened | Enterprise-extended     |
| **Cost**      | Free          | $$$ (subscription)  | $$$$ (full suite)       |
| **Upgrades**  | Manual        | Automated           | Automated + ZDT         |
| **Use Case**  | Labs/Dev      | Lightweight K8s     | Full enterprise PaaS    |