# Fixing "CrashLoopBackOff" for New Users Due to SCC (Security Context Constraints) in OpenShift

When a new `htpasswd` user creates a pod and gets `CrashLoopBackOff`, it's typically due to Security Context Constraints (SCC) blocking the container from running. Here's how to diagnose and fix it:

## 1. Why Does This Happen?

OpenShift enforces strict SCC policies. By default, new users get the `restricted` SCC, which:

- Prevents containers from running as root
- Restricts volume mounts
- Blocks privileged mode

If your pod needs more permissions (e.g., root access, host mounts), it will crash.

---

## 2. Step-by-Step Fix

### A. Check Pod Logs (Why is it Crashing?)
```bash
oc logs <pod-name> -n <namespace>
```
Look for errors like:
- `Permission denied`
- `Unable to write to /var/run`
- `Read-only filesystem`

### B. Check SCC Assigned to the User
```bash
oc get scc
oc describe scc restricted
```
See if the pod's requirements exceed the `restricted` SCC.

### C. Solution 1: Relax SCC for the User (Recommended)

Assign a less restrictive SCC (`nonroot`, `anyuid`, or `privileged`).

#### Option 1: Grant `anyuid` (Allows Run as Any UID, including root)
```bash
oc adm policy add-scc-to-user anyuid -z default -n <namespace>
```
- `-z default` → Applies to the default service account in the namespace.

#### Option 2: Grant `privileged` (Full Access)
> **Warning**: Only for trusted users!
```bash
oc adm policy add-scc-to-user privileged -z default -n <namespace>
```

#### Option 3: Custom SCC (For Fine-Grained Control)
```bash
oc create -f custom-scc.yaml
oc adm policy add-scc-to-user my-custom-scc -z default -n <namespace>
```

### D. Solution 2: Modify Pod to Run as Non-Root

If possible, edit your pod/deployment to:
- Run as a non-root user (e.g., `USER 1001` in Dockerfile).
- Avoid `hostPath` volumes.
- Use OpenShift-friendly settings.

#### Example Deployment Fix:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
    name: my-app
spec:
    template:
        spec:
            securityContext:
                runAsNonRoot: true
                runAsUser: 1001
            containers:
            - name: my-app
                image: nginx
                securityContext:
                    allowPrivilegeEscalation: false
```

### E. Verify Fix

Delete the old pod (it will restart with new permissions):
```bash
oc delete pod <pod-name> -n <namespace>
```

Check new pod status:
```bash
oc get pods -n <namespace>
```

Check logs again:
```bash
oc logs <new-pod-name> -n <namespace>
```

---

## 3. Best Practices

- ✅ Use `anyuid` instead of `privileged` (safer).
- ✅ Avoid running as root (modify containers instead).
- ✅ Use `nonroot` SCC if possible.
- ✅ Limit SCC assignments to only necessary users.

---

## 4. Troubleshooting

### If Pod Still Crashes
- Check SCC assignments:
    ```bash
    oc get scc -o yaml
    ```
- Check audit logs:
    ```bash
    oc adm policy who-can use scc/anyuid
    ```
- Describe pod for errors:
    ```bash
    oc describe pod <pod-name> -n <namespace>
    ```

---

## Final Fix Summary

| Issue                          | Solution                                                                 |
|--------------------------------|---------------------------------------------------------------------------|
| Pod needs root                 | `oc adm policy add-scc-to-user anyuid -z default -n <namespace>`          |
| Pod needs `hostPath`           | Use `emptyDir` or PVC instead                                            |
| Pod needs privileged mode      | `oc adm policy add-scc-to-user privileged -z default -n <namespace>` (Use cautiously!) |
| Container not OpenShift-compatible | Modify Dockerfile to run as non-root (`USER 1001`)                      |

Now your user should be able to run pods without `CrashLoopBackOff`! 🚀