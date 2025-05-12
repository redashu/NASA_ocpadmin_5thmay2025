## Reivision 

### an OCP cluster can have more that one CNI plugin installed

<img src="rev1.png">

### POd -- pod communication across nodes

<img src="rev2.png">

### OCP /k8s is all about API service 

```
1002  oc  get --raw  / 
 1003  oc  get --raw  /healthz
 1004  oc  get --raw  /readyz
 1005  oc  get --raw  /readyz?verbose
 1006  history 

```
## Login to Installed cluster and lets do some verification 

### checking default password for default kubeadmin user 

<img src="login1.png">

## apiServer URL 

<img src="login2.png">

## POD creation using ocp controllers 

<img src="contr1.png">

### checking current project 

```
[ec2-user@ip-172-31-26-148 ~]$ oc project 
Using project "ashu-per" on server "https://api.hellocs.ashutoshh.xyz:6443".
[ec2-user@ip-172-31-26-148 ~]$ 

```

### switching to project 

```
ec2-user@ip-172-31-26-148 ~]$ oc project   default 
Now using project "default" on server "https://api.hellocs.ashutoshh.xyz:6443".
[ec2-user@ip-172-31-26-148 ~]$ 
[ec2-user@ip-172-31-26-148 ~]$ 

```

## creating deployment manifest file using oc 

```
oc  create deployment  ashu-appd   --image  quay.io/jay1123/alpine:amd   --dry-run=client -o yaml

```
### deploy -- controller

```
[ec2-user@ip-172-31-26-148 ~]$ oc  create deployment  ashu-appd   --image  quay.io/jay1123/alpine:amd   --dry-run=client -o yaml  >dep1.yaml 
[ec2-user@ip-172-31-26-148 ~]$ 
[ec2-user@ip-172-31-26-148 ~]$ 
[ec2-user@ip-172-31-26-148 ~]$ oc create -f dep1.yaml 
deployment.apps/ashu-appd created
[ec2-user@ip-172-31-26-148 ~]$ 
[ec2-user@ip-172-31-26-148 ~]$ oc  get deployment 
NAME                READY   UP-TO-DATE   AVAILABLE   AGE
ashu-appd           0/1     1            0           5s
rhope-appd-deploy   0/1     1            0           47s
[ec2-user@ip-172-31-26-148 ~]$ 
[ec2-user@ip-172-31-26-148 ~]$ oc  get deploy
NAME                READY   UP-TO-DATE   AVAILABLE   AGE
ashu-appd           0/1     1            0           12s
rhope-appd-deploy   0/1     1            0           54s
[ec2-user@ip-172-31-26-148 ~]$ oc  get pods
NAME                                 READY   STATUS         RESTARTS      AGE
ashu-appd-59785c4c9-dk4j7            0/1     Completed      2 (14s ago)   16s
rhope-appd-deploy-86bc594f6b-fvt9t   0/1     ErrImagePull   0             58s
[ec2-user@ip-172-31-26-148 ~]$ 
```
