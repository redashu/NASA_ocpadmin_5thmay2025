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

### few more options to check pod info 

```
ec2-user@ip-172-31-26-148 ~]$ oc  get nodes
NAME                          STATUS   ROLES                  AGE     VERSION
ip-10-0-0-18.ec2.internal     Ready    control-plane,master   5h19m   v1.31.7
ip-10-0-3-227.ec2.internal    Ready    control-plane,master   5h17m   v1.31.7
ip-10-0-47-26.ec2.internal    Ready    control-plane,master   5h17m   v1.31.7
ip-10-0-70-128.ec2.internal   Ready    worker                 5h4m    v1.31.7
ip-10-0-77-176.ec2.internal   Ready    worker                 4h57m   v1.31.7
ip-10-0-92-169.ec2.internal   Ready    worker                 5h4m    v1.31.7
[ec2-user@ip-172-31-26-148 ~]$ 
[ec2-user@ip-172-31-26-148 ~]$ oc  get po -o wide
NAME          READY   STATUS    RESTARTS   AGE   IP            NODE                          NOMINATED NODE   READINESS GATES
ashu-pod1     1/1     Running   0          34m   10.129.2.10   ip-10-0-77-176.ec2.internal   <none>           <none>
ashu-pod2     1/1     Running   0          29m   10.131.0.20   ip-10-0-92-169.ec2.internal   <none>           <none>
cm-pod1       1/1     Running   0          25m   10.131.0.21   ip-10-0-92-169.ec2.internal   <none>           <none>
dan-pod1      1/1     Running   0          32m   10.129.2.15   ip-10-0-77-176.ec2.internal   <none>           <none>
harout-pod1   1/1     Running   0          32m   10.129.2.12   ip-10-0-77-176.ec2.internal   <none>           <none>
holly-pod1    1/1     Running   0          26m   10.128.2.42   ip-10-0-70-128.ec2.internal   <none>           <none>
iris-pod1     1/1     Running   0          32m   10.129.2.13   ip-10-0-77-176.ec2.internal   <none>           <none>
jerry-pod1    1/1     Running   0          31m   10.131.0.19   ip-10-0-92-169.ec2.internal   <none>           <none>
lee-pod1      1/1     Running   0          29m   10.128.2.40   ip-10-0-70-128.ec2.internal   <none>           <none>
manuel-pod1   1/1     Running   0          30m   10.129.2.17   ip-10-0-77-176.ec2.internal   <none>           <none>
mjg-pod1      1/1     Running   0          31m   10.129.2.16   ip-10-0-77-176.ec2.internal   <none>           <none>
parsi-pod1    1/1     Running   0          29m   10.129.2.18   ip-10-0-77-176.ec2.internal   <none>           <none>
ptseng-pod1   1/1     Running   0          32m   10.129.2.14   ip-10-0-77-176.ec2.internal   <none>           <none>
russ-pod1     1/1     Running   0          33m   10.129.2.11   ip-10-0-77-176.ec2.internal   <none>           <none>
[ec2-user@ip-172-31-26-148 ~]$ 
[ec2-user@ip-172-31-26-148 ~]$ oc  get po  ashu-pod1  -o wide
NAME        READY   STATUS    RESTARTS   AGE   IP            NODE                          NOMINATED NODE   READINESS GATES
ashu-pod1   1/1     Running   0          36m   10.129.2.10   ip-10-0-77-176.ec2.internal   <none>           <none>
[ec2-user@ip-172-31-26-148 ~]$ 


===>
[ec2-user@ip-172-31-26-148 ~]$ oc  describe  pod  ashu-pod1 
Name:             ashu-pod1
Namespace:        default
Priority:         0
Service Account:  default
Node:             ip-10-0-77-176.ec2.internal/10.0.77.176
Start Time:       Wed, 07 May 2025 17:54:38 +0000
Labels:           run=ashu-pod1
Annotations:      k8s.ovn.org/pod-networks:
                    {"default":{"ip_addresses":["10.129.2.10/23"],"mac_address":"0a:58:0a:81:02:0a","gateway_ips":["10.129.2.1"],"routes":[{"dest":"10.128.0.0...
                  k8s.v1.cni.cncf.io/network-status:
                    [{
                        "name": "ovn-kubernetes",
                        "interface": "eth0",
                        "ips": [


===>
[ec2-user@ip-172-31-26-148 ~]$ oc  logs ashu-pod1

/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default


```

### loging to pod container 

```
[ec2-user@ip-172-31-26-148 ~]$ oc  exec -it ashu-pod1   --  ls /
bin  boot  dev	docker-entrypoint.d  docker-entrypoint.sh  etc	home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
[ec2-user@ip-172-31-26-148 ~]$ 
[ec2-user@ip-172-31-26-148 ~]$ oc  exec -it ashu-pod1   -- /bin/bash 
root@ashu-pod1:/# 
root@ashu-pod1:/# 
root@ashu-pod1:/# ls
bin  boot  dev	docker-entrypoint.d  docker-entrypoint.sh  etc	home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root@ashu-pod1:/# 
root@ashu-pod1:/# exit
exit
[ec2-user@ip-172-31-26-148 ~]$ 
[ec2-user@ip-172-31-26-148 ~]$ 

```

## Rsh in pod ops

```
[ec2-user@ip-172-31-26-148 ~]$ oc  rsh   ashu-pod1    
# 
# id
uid=0(root) gid=0(root) groups=0(root)
# ls 
bin  boot  dev	docker-entrypoint.d  docker-entrypoint.sh  etc	home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
# mkdir hello
# ls
bin  boot  dev	docker-entrypoint.d  docker-entrypoint.sh  etc	hello  home  lib  lib64  media	mnt  opt  proc	root  run  sbin  srv  sys  tmp	usr  var
# exit
[ec2-user@ip-172-31-26-148 ~]$ 


```

### pod delete 

```
ec2-user@ip-172-31-26-148 ~]$ oc  delete  pod ashu-pod1
pod "ashu-pod1" deleted

```

## namespace and project concepts 

<img src="ns1.png">

### correct way to login into ocp cluster permanently

```
[ec2-user@ip-172-31-26-148 ~]$ oc  login -u kubeadmin -p  ""  https://api.nasa-cluster.ashutoshh.xyz:6443 
Login successful.

You have access to 73 projects, the list has been suppressed. You can list all projects with 'oc projects'

Using project "default".
[ec2-user@ip-172-31-26-148 ~]$ 
[ec2-user@ip-172-31-26-148 ~]$ 
[ec2-user@ip-172-31-26-148 ~]$ oc  get pods
No resources found in default namespace.
[ec2-user@ip-172-31-26-148 ~]$ 
[ec2-user@ip-172-31-26-148 ~]$ 
[ec2-user@ip-172-31-26-148 ~]$ 
[ec2-user@ip-172-31-26-148 ~]$ oc  project
Using project "default" on server "https://api.nasa-cluster.ashutoshh.xyz:6443".
[ec2-user@ip-172-31-26-148 ~]$ 

```

### listing all 

```
[ec2-user@ip-172-31-26-148 ~]$ oc  project
Using project "default" on server "https://api.nasa-cluster.ashutoshh.xyz:6443".
[ec2-user@ip-172-31-26-148 ~]$ 
[ec2-user@ip-172-31-26-148 ~]$ oc  projects 
You have access to the following projects and can switch between them with ' project <projectname>':

  * default
    kube-node-lease
    kube-public
    kube-system
    openshift

```

### checking other projects for router and ocp UI 

```
[ec2-user@ip-172-31-26-148 ~]$ oc  get pods -n openshift-ingress 
NAME                              READY   STATUS    RESTARTS   AGE
router-default-788869dc54-d2wxp   1/1     Running   0          5h45m
router-default-788869dc54-p59lg   1/1     Running   0          5h45m
[ec2-user@ip-172-31-26-148 ~]$ 
[ec2-user@ip-172-31-26-148 ~]$ 
[ec2-user@ip-172-31-26-148 ~]$ oc  get route  -n openshift-ingress 
No resources found in openshift-ingress namespace.
[ec2-user@ip-172-31-26-148 ~]$ 
[ec2-user@ip-172-31-26-148 ~]$ 
[ec2-user@ip-172-31-26-148 ~]$ oc projects  | grep -i console 
    openshift-console
    openshift-console-operator
    openshift-console-user-settings
    openshift-network-console
[ec2-user@ip-172-31-26-148 ~]$ oc  get route  -n openshift-console 
NAME        HOST/PORT                                                     PATH   SERVICES    PORT    TERMINATION          WILDCARD
console     console-openshift-console.apps.nasa-cluster.ashutoshh.xyz            console     https   reencrypt/Redirect   None
downloads   downloads-openshift-console.apps.nasa-cluster.ashutoshh.xyz          downloads   http    edge/Redirect        None
[ec2-user@ip-172-31-26-148 ~]$ 
[ec2-user@ip-172-31-26-148 ~]$ oc  get pod  -n openshift-console 
NAME                         READY   STATUS    RESTARTS   AGE
console-8bf858d8b-52djq      1/1     Running   0          5h30m
console-8bf858d8b-rnq56      1/1     Running   0          5h30m
downloads-5bf6d6d998-r8ghb   1/1     Running   0          5h40m
downloads-5bf6d6d998-vh928   1/1     Running   0          5h40m
[ec2-user@ip-172-31-26-148 ~]$ 

```

### gettting yaml file from a running pod 

```
oc  get  po  ashu-podx1   -o yaml 
apiVersion: v1
kind: Pod
metadata:
  annotations:
    k8s.ovn.org/pod-networks: '{"default":{"ip_addresses":["10.129.2.19/23"],"mac_address":"0a:58:0a:81:02:13","gateway_ips":["10.129.2.1"],"routes":[{"dest":"10.128.0.0/14","nextHop":"10.129.2.1"},{"dest":"172.30.0.0/16","nextHop":"10.129.2.1"},{"dest":"169.254.0.5/32","nextHop":"10.129.2.1"},{"dest":"100.64.0.0/16","nextHop":"10.129.2.1"}],"ip_address":"10.129.2.19/23","gateway_ip":"10.129.2.1","role":"primary"}}'
    k8s.v1.cni.cncf.io/network-status: |-
      [{
          "name": "ovn-kubernetes",
          "interface": "eth0",
          "ips": [

```

### history 

```
  209  oc  get  po  ashu-podx1   -o yaml 
  210  oc  get  po  ashu-podx1   -o json 

```

### checking webui details 

```
[ec2-user@ip-172-31-26-148 ~]$ oc  projects | grep console 
    openshift-console
    openshift-console-operator
    openshift-console-user-settings
    openshift-network-console
[ec2-user@ip-172-31-26-148 ~]$ oc  get routes -n openshift-console
NAME        HOST/PORT                                                     PATH   SERVICES    PORT    TERMINATION          WILDCARD
console     console-openshift-console.apps.nasa-cluster.ashutoshh.xyz            console     https   reencrypt/Redirect   None
downloads   downloads-openshift-console.apps.nasa-cluster.ashutoshh.xyz          downloads   http    edge/Redirect        None
[ec2-user@ip-172-31-26-148 ~]$ ls
README.md  hello_ocp  kubectl  oc  openshift-client-linux.tar.gz  openshift-install  openshift-install-linux.tar.gz  pull-secret.txt
[ec2-user@ip-172-31-26-148 ~]$ ls hello_ocp/
auth  metadata.json  terraform.platform.auto.tfvars.json  terraform.tfvars.json  tls
[ec2-user@ip-172-31-26-148 ~]$ ls hello_ocp/auth/
kubeadmin-password  kubeconfig
[ec2-user@ip-172-31-26-148 ~]$ oc whoami
kube:admin
[ec2-user@ip-172-31-26-148 ~]$ oc  get pod  -n openshift-console
NAME                         READY   STATUS    RESTARTS   AGE
console-8bf858d8b-52djq      1/1     Running   0          7h34m
console-8bf858d8b-rnq56      1/1     Running   0          7h34m
downloads-5bf6d6d998-r8ghb   1/1     Running   0          7h44m
downloads-5bf6d6d998-vh928   1/1     Running   0          7h44m
[ec2-user@ip-172-31-26-148 ~]$ 


```

## COntainer networking using SDN 

<img src="net1.png">

### COntainer default bridge network setup 

<img src="net2.png">

## some action with docker networking 

```
[ec2-user@ip-172-31-26-148 ~]$ docker network  ls
NETWORK ID     NAME      DRIVER    SCOPE
1bb03ac4e8c8   bridge    bridge    local
3cf049ddd132   host      host      local
2e61a85a66a0   none      null      local
[ec2-user@ip-172-31-26-148 ~]$ docker network  inspect  1bb03ac4e8c8 
[
    {
        "Name": "bridge",
        "Id": "1bb03ac4e8c8c0c7a4e485483e99441541dfa797e02736e917da0cc95b0ac0eb",
        "Created": "2025-05-07T22:16:24.843934793Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.17.0.0/16",
                    "Gateway": "172.17.0.1"
                }
            ]
        },

```

### lets create container to test 

```
docker  run -itd --name ashuc1  alpine

[ec2-user@ip-172-31-26-148 ~]$ 
[ec2-user@ip-172-31-26-148 ~]$ docker  exec -it ashuc1  sh 
/ # 
/ # ifconfig 
eth0      Link encap:Ethernet  HWaddr 02:42:AC:11:00:02  
          inet addr:172.17.0.2  Bcast:172.17.255.255  Mask:255.255.0.0
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:17 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0 
          RX bytes:1462 (1.4 KiB)  TX bytes:0 (0.0 B)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

/ # ping 172.17.0.6
PING 172.17.0.6 (172.17.0.6): 56 data bytes
64 bytes from 172.17.0.6: seq=0 ttl=127 time=0.110 ms
64 bytes from 172.17.0.6: seq=1 ttl=127 time=0.073 ms
^C
--- 172.17.0.6 ping statistics ---
2 packets transmitted, 2 packets received, 0% packet loss
round-trip min/avg/max = 0.073/0.091/0.110 ms
/ # ping google.com 
PING google.com (172.253.62.102): 56 data bytes
64 bytes from 172.253.62.102: seq=0 ttl=107 time=2.496 ms
64 bytes from 172.253.62.102: seq=1 ttl=107 time=1.942 ms

```

## CNI bridge story in k8s / ocp 

<img src="cni.png">

### pod in ocp can communicate to each other 

```
ec2-user@ip-172-31-26-148 ~]$ oc  run webapp --image nginx --port 80 
pod/webapp created
[ec2-user@ip-172-31-26-148 ~]$ oc get po -o wide
NAME     READY   STATUS    RESTARTS   AGE   IP            NODE                          NOMINATED NODE   READINESS GATES
webapp   1/1     Running   0          5s    10.129.2.29   ip-10-0-77-176.ec2.internal   <none>           <none>
[ec2-user@ip-172-31-26-148 ~]$ 
[ec2-user@ip-172-31-26-148 ~]$ 
[ec2-user@ip-172-31-26-148 ~]$ oc  run webclient  --image alpine --command sleep 1000000
pod/webclient created
[ec2-user@ip-172-31-26-148 ~]$ oc get po -o wide
NAME        READY   STATUS              RESTARTS   AGE   IP            NODE                          NOMINATED NODE   READINESS GATES
russweb     0/1     ContainerCreating   0          2s    <none>        ip-10-0-77-176.ec2.internal   <none>           <none>
webapp      1/1     Running             0          38s   10.129.2.29   ip-10-0-77-176.ec2.internal   <none>           <none>
webclient   1/1     Running             0          5s    10.129.2.30   ip-10-0-77-176.ec2.internal   <none>           <none>
[ec2-user@ip-172-31-26-148 ~]$ oc rsh webclient 
/ # curl http://10.129.2.29
/bin/sh: curl: not found
/ # apk add curl 
fetch https://dl-cdn.alpinelinux.org/alpine/v3.21/main/x86_64/APKINDEX.tar.gz
fetch https://dl-cdn.alpinelinux.org/alpine/v3.21/community/x86_64/APKINDEX.tar.gz
(1/9) Installing brotli-libs (1.1.0-r2)
(2/9) Installing c-ares (1.34.5-r0)
(3/9) Installing libunistring (1.2-r0)
(4/9) Installing libidn2 (2.3.7-r0)
(5/9) Installing nghttp2-libs (1.64.0-r0)
(6/9) Installing libpsl (0.21.5-r3)
(7/9) Installing zstd-libs (1.5.6-r2)
(8/9) Installing libcurl (8.12.1-r1)
(9/9) Installing curl (8.12.1-r1)
Executing busybox-1.37.0-r12.trigger
OK: 12 MiB in 24 packages
/ # 
/ # curl http://10.129.2.29
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
/ # 

```

## Network policy in OCP 

<img src="netpol1.png">


### to define rules using network policy we need to write yaml/json based manifest

<img src="netpol2.png">

### listing all api-resources with its kind and apiversion in ocp 

```
[ec2-user@ip-172-31-26-148 ~]$ oc  api-resources   | grep -i networkpol
networkpolicies                       netpol                                                                                 networking.k8s.io/v1                          true         NetworkPolicy
adminnetworkpolicies                  anp                                                                                    policy.networking.k8s.io/v1alpha1             false        AdminNetworkPolicy
baselineadminnetworkpolicies          banp                                                                                   policy.networking.k8s.io/v1alpha1             false        BaselineAdminNetworkPolicy
[ec2-user@ip-172-31-26-148 ~]$ 

```

## Creating pod yaml using oc command 

```
[ec2-user@ip-172-31-26-148 ~]$ oc run  ashu-podx1  --image nginx  --dry-run=client -o yaml 
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: ashu-podx1
  name: ashu-podx1
spec:
  containers:
  - image: nginx
    name: ashu-podx1
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
[ec2-user@ip-172-31-26-148 ~]$ oc run  ashu-podx1  --image nginx  --dry-run=client -o yaml  >ashupod1.yaml 
[ec2-user@ip-172-31-26-148 ~]$ 

```

### Creating from yaml file 

```
ec2-user@ip-172-31-26-148 ~]$ oc create  -f ashupod1.yaml 
pod/ashu-podx1 created
[ec2-user@ip-172-31-26-148 ~]$ oc  get  pod
NAME          READY   STATUS    RESTARTS   AGE
ashu-podx1    1/1     Running   0          6s
holly-podx1   1/1     Running   0          5s
iris-pod1     1/1     Running   0          3s
[ec2-user@ip-172-31-26-148 ~]$ oc  get  pod -o wide
NAME          READY   STATUS    RESTARTS   AGE   IP            NODE                          NOMINATED NODE   READINESS GATES
ashu-podx1    1/1     Running   0          17s   10.129.2.36   ip-10-0-77-176.ec2.internal   <none>           <none>
holly-podx1   1/1     Running   0          16s   10.129.2.37   ip-10-0-77-176.ec2.internal   <none>           <none>
iris-pod1     1/1     Running   0          14s   10.129.2.38   ip-10-0-77-176.ec2.internal   <none>           <none>
jerry-podx1   1/1     Running   0          8s    10.129.2.39   ip-10-0-77-176.ec2.internal   <none>           <none>
[ec2-user@ip-172-31-26-148 ~]$ 

```

### testing pod to pod communication with web app

```
ec2-user@ip-172-31-26-148 ~]$ oc rsh client 
/ # 
/ # curl  http://10.129.2.36
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
/ # 
/ # exit
[ec2-user@ip-172-31-26-148 ~]$ 

```

### apply default deny to all the pods in current namespace 

```
[ec2-user@ip-172-31-26-148 ~]$ oc create -f default-deny.yaml 
networkpolicy.networking.k8s.io/default-deny created
[ec2-user@ip-172-31-26-148 ~]$ 
[ec2-user@ip-172-31-26-148 ~]$ 
[ec2-user@ip-172-31-26-148 ~]$ oc  get   netpol 
NAME           POD-SELECTOR   AGE
default-deny   <none>         7s
[ec2-user@ip-172-31-26-148 ~]$ 

```
### Creating network policy to allow particular pod 

```
[ec2-user@ip-172-31-26-148 ~]$ mkdir  ashu-yaml
[ec2-user@ip-172-31-26-148 ~]$ ls
 README.md       cm2-pod1.yaml       holly              kubectl          mjgpod1.yaml                    openshift-install-linux.tar.gz   pull-secret.txt
 ashu-yaml       default-deny.yaml   hollypod1.yaml     leepod1.yaml     oc                              poc1                             russpod3.yaml
 ashupod1.yaml   haroutpod1.yaml     irispod1.yaml      manuelpod.yaml   openshift-client-linux.tar.gz   podnew.yaml
 cm1-pod1.yaml   hello_ocp          "jerry'spod.yaml"   md1.yaml         openshift-install               ptsengpod1.yaml
[ec2-user@ip-172-31-26-148 ~]$ ls
 README.md       cm1-pod1.yaml       hello_ocp        irispod1.yaml      manuelpod.yaml   oc                               poc1              russpod3.yaml
 ashu-yaml       cm2-pod1.yaml       holly-yaml      "jerry'spod.yaml"   md1              openshift-client-linux.tar.gz    podnew.yaml
 ashupod1.yaml   default-deny.yaml   hollypod1.yaml   kubectl            md1.yaml         openshift-install                ptsengpod1.yaml
 chris           haroutpod1.yaml     iris-yaml        leepod1.yaml       mjgpod1.yaml     openshift-install-linux.tar.gz   pull-secret.txt
[ec2-user@ip-172-31-26-148 ~]$ 
[ec2-user@ip-172-31-26-148 ~]$ cp  default-deny.yaml   ashu-yaml/
[ec2-user@ip-172-31-26-148 ~]$ 
[ec2-user@ip-172-31-26-148 ~]$ cd ashu-yaml/
[ec2-user@ip-172-31-26-148 ashu-yaml]$ ls
default-deny.yaml
[ec2-user@ip-172-31-26-148 ashu-yaml]$ 


```
### show label 

```
ec2-user@ip-172-31-26-148 ashu-yaml]$ oc  get po  --show-labels
NAME           READY   STATUS    RESTARTS   AGE   LABELS
ashu-podx1     1/1     Running   0          28m   run=ashu-podx1
client         1/1     Running   0          27m   run=client
cm-podx2       1/1     Running   0          28m   run=cm-podx2
harout-podx1   1/1     Running   0          27m   run=harout-podx1
holly-podx1    1/1     Running   0          28m   run=holly-podx1
iris-pod1      1/1     Running   0          28m   run=iris-pod1
jerry-podx1    1/1     Running   0          28m   run=jerry-podx1
manuel-podx1   1/1     Running   0          27m   run=manuel-podx1
md1            1/1     Running   0          28m   run=md1
mjg-podx1      1/1     Running   0          26m   run=mjg-podx1

```

### updating existing info 

```
[ec2-user@ip-172-31-26-148 md1]$ oc apply  -f allow-md1.yaml 
Warning: resource networkpolicies/md1-allow-rule is missing the kubectl.kubernetes.io/last-applied-configuration annotation which is required by oc apply. oc apply should only be used on resources created declaratively by either oc create --save-config or oc apply. The missing annotation will be patched automatically.
networkpolicy.networking.k8s.io/md1-allow-rule configured
[ec2-user@ip-172-31-26-148 md1]$ 
[ec2-user@ip-172-31-26-148 md1]$ oc apply  -f allow-md1.yaml 
networkpolicy.networking.k8s.io/md1-allow-rule configured
[ec2-user@ip-172-31-26-148 md1]$ oc get netpol
NAME                       POD-SELECTOR       AGE
allow-ptsengpod-http       run=ptseng-podx1   5s
allow-testpod-http-chris   run=testpod        2s
ashupod-allow-http         run=ashu-podx1     9m13s
default-deny               <none>             27m
hollypod-allow-http        run=holly-podx1    5m49s
irispod-allow-http         run=iris-pod1      4m30s
jerrypod-allow-http        run=jerry-podx1    111s
md1-allow-rule             run=md1            4m59s
mjgpod-allow-http          run=mjg-podx1      3m57s
[ec2-user@ip-172-31-26-148 md1]$ 

```
