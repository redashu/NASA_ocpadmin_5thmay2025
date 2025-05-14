# OpenShift Virtualization: Comprehensive Guide

## 1. Overview

OpenShift Virtualization (formerly Containerized Data Importer - CDI) integrates VM management into OpenShift Container Platform (OCP) using KubeVirt. It allows running VMs alongside containers, managed via Kubernetes APIs. Ideal for hybrid workloads requiring legacy VM support.

## 2. Core Components

### KubeVirt

Extends Kubernetes to manage VMs. Key sub-components:

- **virt-api**: Exposes KubeVirt APIs.
- **virt-controller**: Manages VM lifecycle (start/stop).
- **virt-handler**: Node-level agent for VM operations.
- **virt-launcher**: Pod that wraps the VM process (Libvirt/QEMU).

### HyperConverged Operator (HCO)

- Manages dependencies (KubeVirt, CDI, Multus) via a single operator.
- Deploys components via a HyperConverged Custom Resource (CR).

### Containerized Data Importer (CDI)

- Imports/exports VM disk images (e.g., QCOW2) into PersistentVolumeClaims (PVCs).
- Uses `DataVolume` CR to automate PVC creation and image population.

### Multus CNI

- Enables attaching multiple network interfaces to VMs using `NetworkAttachmentDefinition`.

### VirtualMachine (VM) CR

- Defines VM specs (CPU, memory, disks, interfaces).
- Managed via `VirtualMachine` and `VirtualMachineInstance` (VMI) CRs.

## 3. Installation

### Prerequisites

- OCP 4.6+ cluster with cluster-admin privileges.
- Nodes with hardware virtualization support (Intel VT/AMD-V) or nested virtualization enabled (for cloud providers).

### Steps

#### Install OpenShift Virtualization Operator

1. Navigate to **Operators → OperatorHub** in the OCP console.
2. Search for "OpenShift Virtualization", click **Install**.
3. Choose namespace `openshift-cnv` and approval strategy (Automatic/Manual).

#### Create HyperConverged CR

```yaml
apiVersion: hco.kubevirt.io/v1beta1
kind: HyperConverged
metadata:
    name: kubevirt-hyperconverged
    namespace: openshift-cnv
spec:
    featureGates:
        enableCommonBootImageImport: true  # Enable importing common OS images
```

Apply with:

```bash
oc apply -f <file>.yaml
```

#### Verify Installation

```bash
oc get pods -n openshift-cnv  # Expect running virt-* and cdi-* pods
oc get hyperconverged -n openshift-cnv  # Check status
```

## 4. Creating a VM

### Step 1: Create a DataVolume (Disk)

Download a Fedora cloud image into a PVC:

```yaml
apiVersion: cdi.kubevirt.io/v1beta1
kind: DataVolume
metadata:
    name: fedora-dv
spec:
    source:
        http:
            url: "https://download.fedoraproject.org/pub/fedora/linux/releases/38/Cloud/x86_64/images/Fedora-Cloud-Base-38-1.6.x86_64.qcow2"
    pvc:
        accessModes: [ReadWriteOnce]
        resources:
            requests:
                storage: 10Gi
```

Apply with:

```bash
oc apply -f fedora-dv.yaml
```

### Step 2: Define the VM

Create a VM referencing the DataVolume:

```yaml
apiVersion: kubevirt.io/v1
kind: VirtualMachine
metadata:
    name: fedora-vm
spec:
    running: true  # Start VM immediately
    template:
        metadata:
            labels:
                kubevirt.io/domain: fedora-vm
        spec:
            domain:
                devices:
                    disks:
                    - name: rootdisk
                        disk: { bus: virtio }
                    interfaces:
                    - name: default
                        masquerade: {}  # Use pod network
                resources:
                    requests:
                        memory: 2Gi
                        cpu: 1
            networks:
            - name: default
                pod: {}  # Attach to pod network
            volumes:
            - name: rootdisk
                dataVolume:
                    name: fedora-dv
```

Apply with:

```bash
oc apply -f vm-fedora.yaml
```

### Step 3: Access the VM

#### Console:

```bash
virtctl console fedora-vm
```

#### Web Console:

Navigate to **Workloads → Virtual Machines** and click **Open Console**.

### Step 4: Verify VM Status

```bash
oc get vmi  # Check VirtualMachineInstance status
oc get vm   # Check VM state (Running/Stopped)
```

## 5. Advanced Configurations

### Networking

Use Multus to attach secondary networks:

```yaml
networks:
- name: secondary-net
    multus:
        networkName: my-nad  # NetworkAttachmentDefinition name
```

### Storage

Use `storageClassName` in `DataVolume` to specify a StorageClass (e.g., `ocs-storagecluster-ceph-rbd`).

### Live Migration

Enable in HyperConverged CR:

```yaml
spec:
    liveMigrationConfig:
        completionTimeoutPerGiB: 800
```

## 6. Troubleshooting

### VM Fails to Start

- Check VMI events: `oc describe vmi/fedora-vm`.
- Ensure DataVolume is populated: `oc describe dv/fedora-dv`.

### Image Import Issues

- Verify URL accessibility and PVC storage size.

### Networking

- Validate `NetworkAttachmentDefinition` and Multus installation.

