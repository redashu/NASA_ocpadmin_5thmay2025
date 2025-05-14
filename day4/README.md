# NASA_ocpadmin_5thmay2025

### some info 

<img src="info.png">

### ocp default CNI bridge 
- can give net inter to vm , pod container 

<img src="info1.png">

### storage in OCP for pod / vm 

<img src="info2.png">

### ocp can use internal or external storage

<img src="info3.png">

### storage in ocp using volume / CSI drivers 

<img src="info4.png">

### storage pv,pvc in ocp 

<img src="info5.png">

### storage class 

<img src="info6.png">


### checking storage class

```
[ec2-user@ip-172-31-26-148 ~]$ oc  get  sc
NAME                PROVISIONER       RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
gp2-csi             ebs.csi.aws.com   Delete          WaitForFirstConsumer   true                   11h
gp3-csi (default)   ebs.csi.aws.com   Delete          WaitForFirstConsumer   true                   11h
[ec2-user@ip-172-31-26-148 ~]$ 


```
