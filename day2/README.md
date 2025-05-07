# NASA_ocpadmin_5thmay2025

<img src="rev1.png">

### openshift varients 

<img src="rev2.png">

### openshift options as product

<img src="rev3.png">


## Lets play with ocp cluster 

### verify ocp client side software 

```
ec2-user@ip-172-31-26-148 ~]$ oc  version 
Client Version: 4.18.10
Kustomize Version: v5.4.2
[ec2-user@ip-172-31-26-148 ~]$ 
[ec2-user@ip-172-31-26-148 ~]$ 
[ec2-user@ip-172-31-26-148 ~]$ kubectl  version 
Client Version: v1.31.1
Kustomize Version: v5.4.2
The connection to the server localhost:8080 was refused - did you specify the right host or port?
[ec2-user@ip-172-31-26-148 ~]$ 
[ec2-user@ip-172-31-26-148 ~]$ 
[ec2-user@ip-172-31-26-148 ~]$ 


```

### understanding auth directory 

<img src="auth1.png">

### using kubeconfig variable to connect ocp/k8s cluster

```
export  KUBECONFIG=/home/ec2-user/hello_ocp/auth/kubeconfig
```

### verify oc connect

```
[ec2-user@ip-172-31-26-148 ~]$ oc whoami
system:admin
[ec2-user@ip-172-31-26-148 ~]$ 
[ec2-user@ip-172-31-26-148 ~]$ oc  cluster-info 
Kubernetes control plane is running at https://api.nasa-cluster.ashutoshh.xyz:6443

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
[ec2-user@ip-172-31-26-148 ~]$ 


```

### verify openshift installed cluster 

```
[ec2-user@ip-172-31-26-148 ~]$ oc  get  nodes
NAME                          STATUS   ROLES                  AGE     VERSION
ip-10-0-0-18.ec2.internal     Ready    control-plane,master   4h12m   v1.31.7
ip-10-0-3-227.ec2.internal    Ready    control-plane,master   4h10m   v1.31.7
ip-10-0-47-26.ec2.internal    Ready    control-plane,master   4h10m   v1.31.7
ip-10-0-70-128.ec2.internal   Ready    worker                 3h57m   v1.31.7
ip-10-0-77-176.ec2.internal   Ready    worker                 3h50m   v1.31.7
ip-10-0-92-169.ec2.internal   Ready    worker                 3h57m   v1.31.7
[ec2-user@ip-172-31-26-148 ~]$ 
[ec2-user@ip-172-31-26-148 ~]$ 
[ec2-user@ip-172-31-26-148 ~]$ 

===>
ec2-user@ip-172-31-26-148 ~]$ oc  version 
Client Version: 4.18.10
Kustomize Version: v5.4.2
Server Version: 4.18.10
Kubernetes Version: v1.31.7

[ec2-user@ip-172-31-26-148 ~]$ oc get --raw / 
{
  "paths": [
    "/.well-known/openid-configuration",
    "/api",
    "/api/v1",
    "/apis",
    "/apis/",
    "/apis/admissionregistration.k8s.io",

===>
[ec2-user@ip-172-31-26-148 ~]$ oc get --raw /readyz?verbose
[+]ping ok
[+]log ok
[+]api-openshift-apiserver-available ok
[+]api-openshift-oauth-apiserver-available ok
[+]informer-sync ok
[+]poststarthook/openshift.io-oauth-apiserver-reachable ok
[+]poststarthook/start-apiserver-admission-initializer ok
[+]poststarthook/quota.openshift.io-clusterquotamapping ok
[+]poststarthook/openshift.io-api-request-count-filter ok
[+]poststarthook/openshift.io-startkubeinformers ok
[+]poststarthook/openshift.io-openshift-apiserver-reachable ok
[+]poststarthook/generic-apiserver-start-informers ok
[+]poststarthook/priority-and-fairness-config-consumer ok

```

## Understanding image to container creation process in CRE

<img src="cre123.png">

## Info about POD 

<img src="pod.png">

### an openshift visual 

<img src="pod1.png">

### creating pod 

```
[ec2-user@ip-172-31-26-148 ~]$ oc  get  pods
No resources found in default namespace.
[ec2-user@ip-172-31-26-148 ~]$ 
[ec2-user@ip-172-31-26-148 ~]$ 
[ec2-user@ip-172-31-26-148 ~]$ oc  run  ashu-pod1    --image nginx  --port 80 
pod/ashu-pod1 created
[ec2-user@ip-172-31-26-148 ~]$ oc  get  pods
NAME        READY   STATUS    RESTARTS   AGE
ashu-pod1   1/1     Running   0          11s
[ec2-user@ip-172-31-26-148 ~]$ 

```
