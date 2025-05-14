# Integrating LDAP with OpenShift for User Authentication

This guide walks you through integrating LDAP with your OpenShift cluster to manage user authentication.

## Prerequisites

- OpenShift cluster with admin privileges
- LDAP server details (hostname, port, bind DN, etc.)
- Proper network connectivity between OpenShift and LDAP server

## Step 1: Prepare LDAP Configuration

### Option A: Using LDAP URL

```bash
oc create secret generic ldap-secret \
    --from-literal=bindPassword='<your_ldap_bind_password>' \
    -n openshift-config
```

### Option B: Using Config File (for more complex setups)

```yaml
# ldap-config.yaml
apiVersion: config.openshift.io/v1
kind: OAuth
metadata:
    name: cluster
spec:
    identityProviders:
    - name: ldap_idp
        mappingMethod: claim
        type: LDAP
        ldap:
            attributes:
                id: ["dn"]
                email: ["mail"]
                name: ["cn"]
                preferredUsername: ["uid"]
            bindDN: "cn=admin,dc=example,dc=com"
            bindPassword:
                name: ldap-secret
            insecure: false
            ca:
                name: ldap-ca-cert
            url: "ldaps://ldap.example.com:636/ou=users,dc=example,dc=com?uid"
```

## Step 2: Apply LDAP Configuration

```bash
# Apply the configuration
oc apply -f ldap-config.yaml

# Or directly via CLI for simple setups:
oc patch oauth cluster --type merge -p '{"spec":{"identityProviders":[{"name":"ldap_provider","mappingMethod":"claim","type":"LDAP","ldap":{"attributes":{"id":["dn"],"email":["mail"],"name":["cn"],"preferredUsername":["uid"]},"bindDN":"cn=admin,dc=example,dc=com","bindPassword":{"name":"ldap-secret"},"insecure":false,"url":"ldaps://ldap.example.com:636/ou=users,dc=example,dc=com?uid"}]}}'
```

## Step 3: Verify LDAP Integration

```bash
# Check authentication operator status
oc get clusteroperator authentication

# Check pods in openshift-authentication namespace
oc get pods -n openshift-authentication

# Check OAuth configuration
oc get oauth cluster -o yaml
```

## Step 4: Test LDAP Login

```bash
# Attempt login with LDAP credentials
oc login -u <ldap_username> -p <ldap_password>

# Check user authorization
oc whoami
oc auth can-i --list
```

## Step 5: Configure Groups (Optional)

### Sync LDAP Groups with OpenShift

```yaml
# ldap-group-sync.yaml
apiVersion: user.openshift.io/v1
kind: LDAPSyncConfig
metadata:
    name: ldap-group-sync
groupUIDNameMapping:
    "cn=admins,ou=groups,dc=example,dc=com": ocp-admins
    "cn=developers,ou=groups,dc=example,dc=com": ocp-developers
groupsQuery:
    baseDN: "ou=groups,dc=example,dc=com"
    scope: sub
    derefAliases: never
    filter: (objectClass=groupOfNames)
    pageSize: 0
groupMembershipAttributes:
- member
```

### Run Group Synchronization

```bash
oc adm groups sync --sync-config=ldap-group-sync.yaml --confirm
```

## Step 6: Configure RBAC for LDAP Users/Groups

```bash
# Grant cluster-admin role to LDAP group
oc adm policy add-cluster-role-to-group cluster-admin ocp-admins

# Grant admin role to LDAP group in specific namespace
oc adm policy add-role-to-group admin ocp-developers -n myproject
```

## Troubleshooting Tips

### Check logs:

```bash
oc logs -n openshift-authentication <oauth-server-pod>
```

### Verify LDAP connectivity:

```bash
oc exec -n openshift-authentication <pod> -- ldapsearch -x -H ldaps://ldap.example.com:636 -b "ou=users,dc=example,dc=com" -D "cn=admin,dc=example,dc=com" -W
```

### Check user synchronization:

```bash
oc get users
oc get groups
```

## Best Practices

- Use secure LDAPS (LDAP over SSL) instead of plain LDAP
- Create a dedicated service account in LDAP for OpenShift binding
- Regularly sync LDAP groups with OpenShift
- Implement proper RBAC for LDAP users/groups
- Monitor authentication logs

## Advanced Configuration Options

### Nested group support:

```yaml
groupsQuery:
    derefAliases: always
```

### Multiple LDAP servers:

```yaml
spec:
    identityProviders:
    - name: ldap_provider1
        type: LDAP
        ldap: {...}
    - name: ldap_provider2
        type: LDAP
        ldap: {...}
```

### Custom certificate authorities:

```bash
oc create configmap ldap-ca-cert --from-file=ca.crt=/path/to/ca.crt -n openshift-config
```