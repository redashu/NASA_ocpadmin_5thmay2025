# Live Migrating Virtual Machines in OpenShift Virtualization

Yes! OpenShift Virtualization (based on KubeVirt) supports live migration of VMs between nodes without downtime. Here's how it works:

## 1. Migration Capabilities

| Feature                     | Supported |
|-----------------------------|-----------|
| Live Migration (no downtime) | ✅ Yes    |
| Cold Migration (shutdown & move) | ✅ Yes |
| Cross-zone migration         | ✅ (If storage allows) |
| Migration policies           | ✅ Yes    |
| Manual or automatic migration | ✅ Both  |

## 2. Prerequisites

### Cluster Requirements:
- OpenShift 4.8+ (recommend 4.12+ for best features)
- Multus CNI configured (for advanced networking cases)
- Shared storage (e.g., RWX PVCs, Ceph, NFS, AWS EFS)

### VM Requirements:
```sh
oc get vmi <vm-name> -o yaml | grep evictionStrategy
```
Should show: `evictionStrategy: LiveMigrate`

## 3. Performing a Live Migration

### Method 1: Using OpenShift Console (UI)
1. Navigate to **Virtualization → Virtual Machines**.
2. Select your running VM.
3. Click ⋮ (Actions menu) → **Migrate Virtual Machine**.
4. Confirm the migration.

### Method 2: Using CLI
```sh
virtctl migrate <vm-name> --namespace=<project>
```

### Method 3: YAML Trigger
Patch the VM:
```sh
oc patch vm <vm-name> --type=merge -p '{"spec":{"running":false}}' 
oc patch vm <vm-name> --type=merge -p '{"spec":{"running":true}}'
```

## 4. Monitoring Migrations

### Check status:
```sh
# List all migrations
oc get vmim -n <namespace> 

# Watch progress
watch oc get vmim -n <namespace>
```

## 5. Migration States
```sh
oc describe vmim <migration-name>
```
- **Pending**: Migration requested
- **Scheduling**: Finding target node
- **TargetReady**: Destination prepared
- **Running**: Data being copied
- **Completed**: Success
- **Failed**: Check events

## 6. Advanced Configuration

### Enable Auto-Live Migration
Add to VM spec:
```yaml
spec:
  evictionStrategy: LiveMigrate
```

### Migration Limits
Configure in HyperConverged CR:
```yaml
apiVersion: hco.kubevirt.io/v1beta1
kind: HyperConverged
metadata:
  name: kubevirt-hyperconverged
spec:
  liveMigrationConfig:
    parallelMigrationsPerCluster: 5
    bandwidthPerMigration: "64Mi"
```

## 7. Troubleshooting

### Common Issues:

#### Stuck migration:
```sh
oc get events -n <namespace> --sort-by=.metadata.creationTimestamp
```

#### Storage problems:
- Ensure PVC is `ReadWriteMany` for shared storage.
- Check Ceph/NFS connectivity.

#### Network issues:
```sh
oc logs -n openshift-cnv <virt-handler-pod>
```

## 8. Limitations
- **Non-shared storage**: Requires cold migration (downtime).
- **GPU-passthrough VMs**: Cannot be live migrated.
- **Very large memory VMs**: May experience brief stun.

## 9. Force Completion (if stuck)
```sh
virtctl migrate-abort <vm-name> -n <namespace>
```