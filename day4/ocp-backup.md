```markdown
# OpenShift On-Prem Backup and Restore Strategies

## Comprehensive Backup Approach

### 1. Cluster Configuration Backup

#### Backup etcd (most critical component):

```bash
# Connect to a control plane node
oc debug node/<master-node>
# Inside debug shell
chroot /host
export ETCDCTL_API=3
etcdctl --endpoints=https://localhost:2379 \
    --cacert=/etc/kubernetes/static-pod-resources/etcd-certs/configmaps/etcd-serving-ca/ca-bundle.crt \
    --cert=/etc/kubernetes/static-pod-resources/etcd-certs/secrets/etcd-all-certs/etcd-peer.crt \
    --key=/etc/kubernetes/static-pod-resources/etcd-certs/secrets/etcd-all-certs/etcd-peer.key \
    snapshot save /var/lib/etcd/etcd-snapshot.db
```

#### Backup cluster resources:

```bash
# Backup all cluster-scoped resources
oc get -o yaml all > cluster-backup.yaml
oc get -o yaml ns > namespaces.yaml
oc get -o yaml clusterroles,clusterrolebindings > cluster-rbac.yaml
oc get -o yaml storageclasses > storageclasses.yaml
```

### 2. Application Data Backup

#### Backup persistent volumes:

```bash
# List all PVCs in cluster
oc get pvc --all-namespaces -o yaml > pvc-backup.yaml

# For each PVC, create a backup (example using rsync)
for ns in $(oc get ns -o jsonpath='{.items[*].metadata.name}'); do
    for pvc in $(oc get pvc -n $ns -o name); do
        oc rsync $pvc:/data/ /backup/$ns/$pvc/
    done
done
```

### 3. Configuration Backup

```bash
# Backup OpenShift configuration
oc get -o yaml cm,secret -n openshift-config > openshift-config-backup.yaml
oc get -o yaml oauth cluster > oauth-backup.yaml
```

---

## Restore Procedures

### 1. ETCD Restore

```bash
# Stop all control plane services
sudo -i
systemctl stop kubelet
mv /etc/kubernetes/manifests/ /etc/kubernetes/manifests.stopped

# Restore snapshot
etcdctl snapshot restore /backup/etcd-snapshot.db \
    --data-dir /var/lib/etcd/restored \
    --initial-cluster "master1=https://master1.example.com:2380" \
    --initial-advertise-peer-urls https://master1.example.com:2380 \
    --name master1

# Replace etcd data
mv /var/lib/etcd/member /var/lib/etcd/member.old
mv /var/lib/etcd/restored/member /var/lib/etcd/

# Restart services
mv /etc/kubernetes/manifests.stopped /etc/kubernetes/manifests
systemctl start kubelet
```

### 2. Cluster Resource Restore

```bash
# Restore cluster resources
oc apply -f cluster-backup.yaml
oc apply -f namespaces.yaml
oc apply -f cluster-rbac.yaml
oc apply -f storageclasses.yaml
```

### 3. Application Data Restore

```bash
# For each PVC, restore from backup
for ns in $(ls /backup/); do
    for pvc in $(ls /backup/$ns/); do
        oc rsync /backup/$ns/$pvc/ $pvc:/data/
    done
done
```

---

## Backup Strategies Comparison

| Strategy          | Scope                 | Tools         | Frequency       | Retention |
|--------------------|-----------------------|---------------|-----------------|-----------|
| ETCD Snapshot      | Cluster state         | etcdctl       | Daily           | 7 days    |
| Resource YAMLs     | Kubernetes objects    | oc get        | Daily           | 30 days   |
| PVC Backup         | Application data      | rsync/velero  | Application-specific | Varies |
| Configuration      | OAuth, operators      | oc get        | Weekly          | 90 days   |

---

## Recommended Tools

### Velero (for application-level backups):

```bash
# Install Velero
velero install \
    --provider aws \
    --plugins velero/velero-plugin-for-aws:v1.0.0 \
    --bucket ocp-backups \
    --secret-file ./credentials-velero \
    --use-volume-snapshots=false \
    --backup-location-config region=minio,s3ForcePathStyle=true,s3Url=http://minio.example.com:9000

# Create backup
velero backup create cluster-backup --include-namespaces=*
```

### OpenShift Backup Operator (Tech Preview):

```yaml
# Create Backup CR
apiVersion: velero.io/v1
kind: Backup
metadata:
    name: daily-backup
spec:
    includedNamespaces: ['*']
    storageLocation: default
    ttl: 720h
```

---

## Best Practices

- Test your backups regularly with restore drills.
- Document your restore procedures in detail.
- Store backups off-cluster with proper encryption.
- Monitor backup jobs for failures.
- Consider application quiescence for consistent backups.

---

## Disaster Recovery Plan

1. **Priority 1**: Restore etcd (cluster state).
2. **Priority 2**: Restore critical operators.
3. **Priority 3**: Restore core infrastructure (DNS, networking).
4. **Priority 4**: Restore applications in order of business importance.
```