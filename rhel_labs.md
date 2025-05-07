## Steps to Follow

### Login to Student Machine

#### Perform All the Operations

```bash
sudo yum install chromium -y
```

### Open Chromium Browser and Download Pull Secret from Hybrid Cloud Platform

[Red Hat OpenShift Hybrid Cloud Platform](https://cloud.redhat.com/openshift/install/metal/user-provisioned)

### Change Secret to JSON Format for Better Editing

```bash
python3 -m json.tool Downloads/pull-secret > pull-secret.json
```

### It Will Look Like This — Change `quay.io` to Local Registry

![Pull Secret Example](secret1.png)

### Changes You Can Make Are Given Below

```json
"nexus-registry-int.apps.tools-apac150.prod.ole.redhat.com": {
    "auth": "",
    "email": ""
}
```

### Finally Convert This to Installer-Supported Format Using `jq` Tool

```bash
cat pull-secret.json | jq . -c > pull-secret-oneline.json
```

### Transfer This to Utility Server as Lab User

```bash
scp pull-secret-oneline.json lab@utility:
```

---

## Connecting to Utility Server

### From Student Terminal, Connect to Utility Server

```bash
ssh lab@utility
```

### Installing Podman

```bash
sudo yum install podman -y
```

### Verify Pull-Secret by Pulling Some Images Using Podman

```bash
sudo podman pull --authfile ~/pull-secret-oneline.json nexus-registry-int.apps.tools-apac150.prod.ole.redhat.com/openshift/ocp4:4.6.4-x86_64
```

### Pulling from Red Hat Registry

```bash
sudo podman pull --authfile ~/pull-secret-oneline.json registry.redhat.io/ubi8/ubi:latest
```

### Verify Images Download

```bash
sudo podman images
```

Example Output:

```
REPOSITORY                                                     TAG            IMAGE ID       CREATED        SIZE
registry.redhat.io/ubi8/ubi                                    latest         3269c37eae33   8 weeks ago    208 MB
nexus-registry-int.apps.tools-na150.prod.ole.redhat.com/openshift/ocp4   4.6.4-x86_64   26f7cd4cf1fb   2 months ago   322 MB
```

## Verify DNS , HTTPD  and DHCP service 

```
systemctl status dhcpd  named  httpd 
```

###  Creating some directories in httpd service location to be served 

```
sudo mkdir -p /var/www/html/openshift4/images
sudo mkdir -p /var/www/html/openshift4/4.6.4/ignitions

```

### Change contenxt as per Selinux standard 

```
sudo restorecon -Rv /var/www/html/openshift4

```
### verify created directory structure 

```
sudo tree /var/www/html/
 /var/www/html/
 └── openshift4
    ├── 4.6.4
    │   └── ignitions
    └── images

```

### Download some bootstrap server images in above location 

```
ocp_maj=4.6;rhcos_ver=4.6.1
mirror=https://mirror.openshift.com/pub/openshift-v4
baseurl=${mirror}/dependencies/rhcos/${ocp_maj}/${rhcos_ver}


sudo wget ${baseurl}/rhcos-${rhcos_ver}-x86_64-live-rootfs.x86_64.img -P /var/www/html/openshift4/images


sudo wget ${baseurl}/rhcos-${rhcos_ver}-x86_64-live-kernel-x86_64 -P /var/www/html/openshift4/images

sudo wget ${baseurl}/rhcos-${rhcos_ver}-x86_64-live-initramfs.x86_64.img -P /var/www/html/openshift4/images

```

### verify download 

```
ls /var/www/html/openshift4/images
```

## For cluster provisioning time to check and modify haproxy server
### HAproxy gonna act as LB for installation purpose during HA based installation

```
systemctl status haproxy
```

### making changes in config details 

```
sudo vi /etc/haproxy/haproxy.cfg 

#---------------------------------------------------------------------
# round robin balancing for RHOCP Kubernetes API Server
#---------------------------------------------------------------------
 frontend k8s_api
   bind *:6443
   mode tcp
   default_backend k8s_api_backend
 backend k8s_api_backend
   balance roundrobin
   mode tcp
   server bootstrap 192.168.50.9:6443 check
   server master01 192.168.50.10:6443 check
   server master02 192.168.50.11:6443 check
   server master03 192.168.50.12:6443 check

# ---------------------------------------------------------------------
# round robin balancing for RHOCP Machine Config Server
# ---------------------------------------------------------------------
 frontend machine_config
   bind *:22623
   mode tcp
   default_backend machine_config_backend
 backend machine_config_backend
   balance roundrobin
   mode tcp
   server bootstrap 192.168.50.9:22623 check
   server master01 192.168.50.10:22623 check
   server master02 192.168.50.11:22623 check
   server master03 192.168.50.12:22623 check

# ---------------------------------------------------------------------
# round robin balancing for RHOCP Ingress Insecure Port
# ---------------------------------------------------------------------
 frontend ingress_insecure
   bind *:80
   mode tcp
   default_backend ingress_insecure_backend
 backend ingress_insecure_backend
   balance roundrobin
   mode tcp
   server worker01 192.168.50.13:80 check
   server worker02 192.168.50.14:80 check

# ---------------------------------------------------------------------
# round robin balancing for RHOCP Ingress Secure Port
# ---------------------------------------------------------------------
 frontend ingress_secure
   bind *:443
   mode tcp
   default_backend ingress_secure_backend
 backend ingress_secure_backend
   balance roundrobin
   mode tcp
   server worker01 192.168.50.13:443 check
   server worker02 192.168.50.14:443 check

```

### validate config changes

```
haproxy -c -f /etc/haproxy/haproxy.cfg
```
### reload haproxy service 

```
sudo systemctl reload haproxy

sudo systemctl status haproxy
```

# COnfigure TFTP service to boot machines 

### Download tftp boot image , ign files 

```
sol_url=http://classroom.example.com/materials/solutions

sudo wget $sol_url/pxelinux.cfg/01-52-54-00-00-32-09 -P /var/lib/tftpboot/pxelinux.cfg/
sudo wget $sol_url/pxelinux.cfg/01-52-54-00-00-32-0a -P /var/lib/tftpboot/pxelinux.cfg/
sudo wget $sol_url/pxelinux.cfg/01-52-54-00-00-32-0b -P /var/lib/tftpboot/pxelinux.cfg/
sudo wget $sol_url/pxelinux.cfg/01-52-54-00-00-32-0c -P /var/lib/tftpboot/pxelinux.cfg/
sudo wget $sol_url/pxelinux.cfg/01-52-54-00-00-32-0d -P /var/lib/tftpboot/pxelinux.cfg/
sudo wget $sol_url/pxelinux.cfg/01-52-54-00-00-32-0e -P /var/lib/tftpboot/pxelinux.cfg/

```

### verify the downloads

```
tree /var/lib/tftpboot/pxelinux.cfg/
/var/lib/tftpboot/pxelinux.cfg/
├── 01-52-54-00-00-32-09
├── 01-52-54-00-00-32-0a
├── 01-52-54-00-00-32-0b
├── 01-52-54-00-00-32-0c
├── 01-52-54-00-00-32-0d
├── 01-52-54-00-00-32-0e
└── 01-example

```

## lets starting creating  install-config file to create ocp cluster with HA option

### installing software 

```
mirror="https://mirror.openshift.com/pub/openshift-v4/clients"

wget ${mirror}/ocp/4.6.4/openshift-client-linux-4.6.4.tar.gz

wget ${mirror}/ocp/4.6.4/openshift-install-linux-4.6.4.tar.gz

sudo tar -xvf openshift-client-linux-4.6.4.tar.gz -C /usr/bin/
sudo tar -xvf openshift-install-linux-4.6.4.tar.gz -C /usr/bin/

```

### verify installation  that is matches with 4.6.4

```
 oc version
 
 openshift-install version
```

### creating ssh key with lab user 

```
ssh-keygen -t rsa -b 4096 -N '' -f .ssh/ocp4upi
```

### Now creating directory and using openshift-installer command 

```
mkdir ocp4upi && cd ocp4upi

wget http://classroom.example.com/materials/solutions/ocp4upi/install-config.yaml

```

### Now time to modify install-config.yaml file 
- paste ssh-key public key
- paste pull-secret-online.json
- update -- nexus-registry-int.apps.tools-apac150.prod.ole.redhat.com

### copy back up 
```
cp install-config.yaml /home/lab/
```

### Creating manifest files

```
openshift-install create manifests --dir=.

INFO Consuming Install Config from target directory
INFO Manifests created in: manifests and openshift

====>
openshift-install create ignition-configs --dir=.
```

## Note: it should look like given below 

<img src="lab1.png">

### Copy Ign files to httpd location 

```
sudo cp *.ign /var/www/html/openshift4/4.6.4/ignitions/
sudo chmod +r /var/www/html/openshift4/4.6.4/ignitions/*.ign

```

## Now boot all the machines using console 
- bootstrap 
- master[01-03]
- worker[01-02]

### if boot is complete ping them with name and ip from utility server

```
ping bootstrap.ocp4.example.com

ping master01.ocp4.example.com

ping master02.ocp4.example.com

ping master03.ocp4.example.com

ping worker01.ocp4.example.com

ping worker02.ocp4.example.com

```

### Now start bootstrap-complete process can take 20 t0 30 minutes

Note: - if everything is good it can be ready in 2 to 5 minutes also 

```
openshift-install --dir=./ocp4upi wait-for bootstrap-complete

```

### check below output 

<img src="boot.png">

## Now remove bootstrap entry from haproxy.cfg and reload service 

```
sudo vi /etc/haproxy/haproxy.cfg 

#---------------------------------------------------------------------
# round robin balancing for RHOCP Kubernetes API Server
#---------------------------------------------------------------------
 frontend k8s_api
   bind *:6443
   mode tcp
   default_backend k8s_api_backend
 backend k8s_api_backend
   balance roundrobin
   mode tcp
   server bootstrap 192.168.50.9:6443 check # remove this line 
   server master01 192.168.50.10:6443 check
   server master02 192.168.50.11:6443 check
   server master03 192.168.50.12:6443 check

# ---------------------------------------------------------------------
# round robin balancing for RHOCP Machine Config Server
# ---------------------------------------------------------------------
 frontend machine_config
   bind *:22623
   mode tcp
   default_backend machine_config_backend
 backend machine_config_backend
   balance roundrobin
   mode tcp
   server bootstrap 192.168.50.9:22623 check # remove this line 
   server master01 192.168.50.10:22623 check
   server master02 192.168.50.11:22623 check
   server master03 192.168.50.12:22623 check

```

### reload service 

```
sudo systemctl reload haproxy
```

## Time verify bootstrap ocp cluster 

```
[lab@utility ~]$   KUBECONFIG=~/ocp4upi/auth/kubeconfig 

## testing credentials 

[lab@utility ~]$ oc whoami
system:admin

### test master node 

[lab@utility ~]$ oc get nodes
master01   Ready    master   76m   v1.19.0+9f84db3
master02   Ready    master   70m   v1.19.0+9f84db3
master03   Ready    master   69m   v1.19.0+9f84db3

```

### Now lets do final installation step to complete everything 

```
[lab@utility ~]$ openshift-install --dir=ocp4upi wait-for install-complete --log-level=debug

```

### if required you need to approve csr manually 

```
oc get csr # note is showing any pending then 

===>
oc get csr -o go-template='{{range .items}}{{if not .status}}{{.metadata.name}}{{"\n"}}{{end}}{{end}}' | xargs oc adm certificate approve
```






