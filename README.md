# Misc tech notes

## OpenShift / Kubernetes
```sh
oc login --token=[token from the console] --server=https://api.your-openshift-cluster.net:[port]

oc project [project_name]

oc get pods --namespace=[namespace]

oc status

oc config get-contexts # show contexts defined in ~/.kube/config

oc config use-context [context name] # set an active context for commands

oc get namespaces

oc logs -f [podname]

oc get claim --all-namespaces

helm list -a

oc apply -f ./[job_definition].yml

kubectl create job --from=[existing_job_defintion] [job_name]
```

#### K8s The Hardway Tutorial
https://github.com/kelseyhightower/kubernetes-the-hard-way
#### Deploying the Kubernetes Dashboard
https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/
#### Creating an admin user for the dashboard
https://github.com/kubernetes/dashboard/blob/master/docs/user/access-control/creating-sample-user.md
#### Misc Operations
```sh
# Deploy a single container as a Kubernetes pod and run a bash shell
PODNAME=$(whoami)-$(uuidgen|cut -c1-5|tr '[:upper:]' '[:lower:]')
kubectl run $PODNAME --image=some.image.registry.whatever/path/to/image_name:latest -it --restart=Never -- /bin/bash

# Delete the pod
kubectl delete pod $PODNAME
```

## Google Cloud

metadata service: 169.254.169.254
### Query metadata service from a VM:
```sh
curl "http://metadata.google.internal/computeMetadata/v1/instance/image" -H "Metadata-Flavor: Google"
```

### Environment Configuration
```sh
gcloud config configurations list
gcloud config configurations activate [config name]
gcloud config configurations create [config name]
glcoud auth login
gcloud auth list
gcloud config set accessibility/screen_reader false
gcloud config set project [project]
gcloud config set compute/region [region]
gcloud compute project-info add-metadata --metadata google-compute-default-region=[region]
```
### ssh to VMs
```sh
gcloud compute config-ssh #configure ~/.ssh/config file with all VM instances in the current project
gcloud compute ssh vm-name
gcloud compute os-login ssh-keys add --key="($ssh-add -L | grep publickey)" --project=$PROJECT
```
### Deploy a Compute Engine VM
```
#!/bin/bash

VM_PREFIX=vm
IMAGE_FAMILY=debian-12
RND_STRING=$(echo $RANDOM|md5sum|cut -c1-4)
GCP_ZONE=us-central1-b
VM_NAME=$VM_PREFIX-$IMAGE_FAMILY-$RND_STRING
IMAGE_PROJECT=debian-cloud

# Deploy Compute Engine VM instance
gcloud compute instances create $VM_NAME \
        --image-project=$IMAGE_PROJECT \
        --image-family=$IMAGE_FAMILY \
        --boot-disk-type=pd-standard \
        --boot-disk-size=29G \
        --machine-type=e2-micro \
        --preemptible \
        --zone=$GCP_ZONE
```
### Managing Networks
```sh
# Create a VPC and subnet
gcloud compute networks create [VPC_network_name]] --project=[project_name] --subnet-mode=custom --mtu=1460 --bgp-routing-mode=regional
gcloud compute networks subnets create [subnet name]] --[project_name] --range=[0.0.0.0/0]] --stack-type=IPV4_ONLY --network=managementnet --region=[region]]

# View VPN Networks and Subnets
gcloud compute networks list
gcloud compute networks subnets list --sort-by=NETWORK

# Create a firewall rule to allow ssh,rdp, and icmp
gcloud compute --project=[project]] firewall-rules create [rule_name] --direction=INGRESS --priority=1000 --network=[VPN_network_name]] --action=ALLOW --rules=tcp:22,tcp:3389,icmp --source-ranges=0.0.0.0/0

# View firewall rules
gcloud compute firewall-rules list --sort-by=NETWORK
```
### Cloud DNS
```sh
# Create a managed DNS Zone
gcloud dns managed-zones create example --description=test --dns-name=example.com --networks=default --visibility=private

# Create a cloud DNS routing policy
gcloud dns record-sets create geo.example.com \
--ttl=5 --type=A --zone=example \
--routing-policy-type=GEO \
--routing-policy-data="us-central1=[IP_Address];europe-west1=[IP_Address]P"

# List DNS Zone records
gcloud dns record-sets list --zone=example
```

## iperf3 examples
```
# server
iperf3 -s

#client UDP test
iperf3 -c [server] -u -i 1 -b 200m -P 4 -V -t 60
# Where c= client mode
# -u = UDP
# -i = seconds between throughput reports
# -b = bitrate in bits/sec per stream (if -P is set)
# -P = number of parallel streams
# -V = verbose
# -t = time in seconds to transmit

#client TCP test
iperf3 -c [server] -P 4 -V -t 60
# Where c= client mode
# -P = number of parallel streams
# -V = verbose
# -t = time in seconds to transmit

# Capturing output with tcpdump
sudo tcpdump -i [interface] \(src host [ip] or src host [ip] or dst host [ip] or dst host [ip]\) \
and port 5201 -s 128 -c 10000 -n -w 'outputfile.pcap' -Z [user you want to be able to read the dump]

```
## netstat: show listening ports
```
netstat -plnt
```

## tmux
crtl + b [command] (or : for tmux command prompt)

ctrl + b, d (detach)

tmux ls

tmux a -t [session] (attach to a specific session)

tmux rename-session -t[session] [new_session_name]

#### Managing Panes
ctrl + b, " (split horizontal)

ctrl + b, % (split vertically)

ctrl + b, [arrow] (navigate panes)

ctrl + b, :, setw synchronize-panes [on,off]

#### Enable mouse scroll
ctrl + b, :, set -g mouse on

## Docker
https://docker-curriculum.com/
```sh
docker login

docker pull ubuntu:latest

docker images

docker search [string]

docker run busybox echo "hello from busybox"

docker ps -a

docker rm [container_id]
docker rm $(docker ps -a -q -f status=exited)
docker container prune

docker run -it busybox sh

docker run -d -P --name static-site prakhar1989/static-site
docker port static-site # see ports now available on localhost

docker run -p 8888:80 prakhar1989/static-site # specifcy custom ports

docker stop [container id]

#Dockerfile example
FROM ubuntu:latest
# USER root
RUN apt-get -y update
RUN apt-get install -y busybox
# ADD ./supervisord/conf.d/* $SCPATH/ # copy a file

#Deploying a flask app example
git clone https://github.com/prakhar1989/docker-curriculum.git
cd docker-curriculum/flask-app
docker build -t [username]]/catnip .
docker run -p 8888:5000 [username]/catnip
# App will now be available on http://localhost:8888

docker push [user_name]/[container_name] # publish a container to the registry

docker build -t [image name] .

# Stop all docker processes
for proc in $(docker ps |grep -v CONT|awk '{print $1}'); do docker kill $proc; done

```
## Hashicorp Vault

```sh
export VAULT_ADDR="https://your-vault-instance.net:8200/" # set vault server address
vault login (copy token from the UI)
vault kv get some/vaultpath/secrets # show key/value pairs under the path some/vaultpath/secrets
vault kv get -mount=some vaultpath/secrets # show key/value pairs under the path some/vaultpath/secrets
VALUE=$(echo $RANDOM | md5 | head -c 20)
vault kv patch some/vaultpath/secrets token="$VALUE" # set the key "token" and the value "$VALUE" under the path some/vaultpath/secrets
```

## GitHub / git
#### Personal Access Token
In the github.com UI go to: Profile, Settings, Developer settings
*Personal Access Token
*Generate New Token
    
Create ~/.netrc file:
```
machine github.com 
login [username] 
password [personal access token]
```

git stash to resolve push conflicts
```
git stash # caches local changes
git pull # syncs from origin
git stash pop # restores local changes
```
![git cheat sheet](/images/gitcheatsheet.jpeg)

## Powers of 2
```
2^0 = 1
2^1 = 2
2^2 = 4
2^3 = 8
2^4 = 16
2^5 = 32
2^6 = 64
2^7 = 128
2^8 = 256
2^9 = 512
2^10 = 1024
2^11 = 2048
2^12 = 4096
2^13 = 8192
2^14 = 16384
2^15 = 32768
2^16 = 65536
2^17 = 131072
2^18 = 262144
2^19 = 524288
```
## ansible
```
ansible --version
ansible-config dump --only-changed

# ansible adhoc commands
ansible [host pattern] -m [module] -a [module arguments] -i [inventory file]
ansible all -m ping
ansible -m user -a 'name=newuser uid=4000 state=present' web1
ansible -m user -a 'name=newuser uid=4000 state=absent' web1
ansible all -m service -a "state=restarted name=sshd"
ansible all -m package -a "name=httpd state=present"

#ansible modules
ansible-doc -l
ansible-doc ping

# Quick and dirty ansible environment
cat 192.168.1.200 > hosts
ansible -e 'ansible_python_interpreter=$PATH_TO_PYTHON_ON_REMOTE' -i ./hosts --user $REMOTE_USER all -m ping
```

## ssh
```
# Generate an ssh key
ssh-keygen -t rsa -f ~/.ssh/mynewkey -C myusername -b 2048

# Forward local port 2222 to 443 on remote host
ssh -L 2222:127.0.0.1:443 remote-host

# copy ssh key to another host
ssh-copy-id myuser@anotherhost

# ssh via a proxy host
ssh -J [proxy_host] [user]@[destination_host]

# Create an ssh proxy connection (add to ~/.ssh/config)
Host [connection name]
    ProxyCommand ssh -q %r@[proxy host] 'exec nc [target host]] %p'
    User [target host user]

# Alternative config
Host [target_host]
    Hostname [target_hostname_or_ip]
    User [target_user]
    ProxyJump [proxy_host]

```

## RHEL 8 Swapfile create
```
dd if=/dev/zero of=/swapfile bs=1024 count=65536 # count = size of swapfile in MB * 1024
mkswap / swapfile
chmod 600 /swapfile
systemctl daemon-reload
swapon /swapfile
free -h # verify swap is active
```
#### Add to /etc/fstab
```
/swapfile swap swap defaults 0 0
```

## LVM
```
# Creating an LVM volume 
parted -s /dev/sdb mklabel gpt
parted -s /dev/sdb mkpart primary 1MiB 257MiB #creates a 256MiB partition
parted -s /dev/sdb set 1 lvm on #sets partition 1 type to lvm
parted -s /dev/sdb mkpart primary 258MiB 514MiB
parted -s /dev/sdb set 2 lvm on
pvcreate /dev/sdb1 /dev/sdb2
vgcreate server01_vg /dev/sdb1 /dev/sdb2
[pv,vg,lv]display
lvcreate -n server01_lv -L 1G server01_vg
mkfs -t /dev/server01_vg/server01_lv #add fstab entry DEVICE not UUID!!
mount -a

#Extending an LVM volume
parted -s /dev/sdb mkpart primary 540MB 796MB
parted -s /dev/sdb set 3 lvm on
pvcreate /dev/sdb3
vgextend server01_vg /dev/sdb3
lvextend -r -L +256MB /dev/server01_vg/server01_lv
Reducing an LVM volume
vgreduce server01_vg /dev/sdb3
```

## Oneshot systemd service
```
cat << EOF > /etc/systemd/system/myoneshotservice.service
[Unit]
Description=Oneshot Service
Wants=network-online.target
After=network-online.target

[Service]
Type=oneshot
ExecStart=/opt/myoneshotservice.sh
RemainAfterExit=true
StandardOutput=journal

[Install]
WantedBy=multi-user.target
EOF
systemctl enable myoneshotservice.service
```

## Linux Performance Analysis
(From Brendan Gregg's [Linux Performance Analysis in 60000 Milliseconds](https://www.brendangregg.com/Articles/Netflix_Linux_Perf_Analysis_60s.pdf))
```
uptime
dmesg | tail
vmstat 1
mpstat -P ALL 1
pidstat 1
iostat -xz 1
free -m
sar -n DEV 1
sar -n TCP,ETCP 1
top
```
## libvirt Examples

### Image Locations
```
/var/lib/libvirt/boot # libvirt boot images
/var/lib/libvirt/images/ # libvirt disk images
```
### Installing a VM with graphics<br> 
#### (Note: Connect to the VNC port once the domain starts)
```
virt-install --name ubuntu01 --ram=2048 \
    --vcpus=1 --cpu host --hvm \
    --disk path=/var/lib/libvirt/images/ubuntu01,size=8 \
    --cdrom /var/lib/libvirt/boot/ubuntu-22.04.2-live-server-amd64.iso \
    --graphics vnc
```

### Installing a headless VM<br>
```
virt-install --name debian12 --ram=2048 \
    --vcpus=2 --cpu host --hvm \
    --disk path=ath=/var/lib/libvirt/images/debian12,size=100 \
    --location /var/lib/libvirt/boot/debian-12.5.0-amd64-netinst.iso \
    --osinfo debian12 \
    --network network=default \
    --graphics none \
    --console pty,target_type=serial \
    --extra-args "console=ttyS0"

# Connect/reconnect to the console
virsh console debian12
```

### Configure VM to autostart
```
virsh autostart [domain]
```

### Manage domains (VMs)
```
virsh list --all
virsh shutdown --domain VM_NAME
virsh destroy --domain VM_NAME # forces removal of a domain
```

### Connect via VNC port 
(Where "host-a" = libvert host, "host-b" = VM, "host-c" = VNC host)

#### From host A, get the VNC port for host-b:
```sudo virsh dumpxml host-b |grep vnc```

#### From host-c, port forward a local port to host-b's VNC port:
```ssh user@host-a -L [VNC port]:127.0.0.1:[VNC port]```

#### From host-c connect with VNC using 127.0.0.1:[VNC port]

### Get Network Information
```
virsh net-list # show networks

virsh net-dhcp-leases default # show DHCP leases for network "default"
```

### Add a disk to a VM
```
qemu-img create -f raw /var/lib/libvirt/images/ubuntu03-disk3-1G 1G

virsh attach-disk ubuntu03 --source /var/lib/libvirt/images/ubuntu03-disk3-1G --target vdb --persistent
```

## LUKS encryption on Unbuntu 20.04
### Create a file system on a LUKS encrypted device
```
export DEVICE=/dev/vdd
export MOUNTPOINT=/encrypted_vdd
export LUKS_NAME=luks-vdd
export PASS_FILE=/root/phrase.txt

apt -y install clevis clevis-luks clevis-systemd
systemctl enable clevis-luks-askpass.path

openssl rand -hex 256 > $PASS_FILE;chmod 400 $PASS_FILE
cryptsetup -q luksFormat $DEVICE $PASS_FILE
cryptsetup luksDump $DEVICE # to view the LUKS header

# Test tang and get signing key
echo "This is the message." > plain.txt
clevis encrypt tang '{"url":"http://tang01"}' < plain.txt > secret.jwe
clevis decrypt < secret.jwe

clevis luks bind -f -k $PASS_FILE -d $DEVICE tang '{"url":"http://tang01","thp":"$SIGNING_KEY"}' 

cryptsetup luksDump $DEVICE # there should be a keyslot using clevis

# On the NBDE client, unlock the device and configure to mount at boot
clevis luks unlock -d $DEVICE -n $LUKS_NAME

mkfs.xfs /dev/mapper/$LUKS_NAME #create a file system
echo "$LUKS_NAME $DEVICE none _netdev" >> /etc/crypttab
echo "/dev/mapper/$LUKS_NAME $MOUNTPOINT xfs defaults,_netdev 0 2" >> /etc/fstab
mkdir $MOUNTPOINT
mount -a
```

### Rotating tang keys
```
# from the tang server
cd /var/db/tang
for file in $(ls -1 *.jwk); do mv $file .$file; done
/usr/libexec/tangd-keygen /var/db/tang
tang-show-keys # to display the new signing key

# update bindings on the NBDE client
clevis luks list -d $DEVICE
clevis luks report -d $DEVICE # updates the key
```
## Analyze [RHEL 8 kdump output](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/assembly_dumping-a-crashed-kernel-for-later-analysis_configuring-basic-system-settings)
```
crash /usr/lib/debug/lib/modules/$KERNEL/vmlinux /var/crash/$DUMP/vmcore
# crash commands
crash> log # display kernel message buffer
crash> bt # display a backtrace
crash> ps # display processes
crash> vm # display virtual memory information
crash> files # display information about open files
```
## Analyze [Red Hat SOS](https://access.redhat.com/solutions/3592) Output
### [xsos](https://github.com/ryran/xsos)
```
dnf -y install http://people.redhat.com/rsawhill/rpms/latest-rsawaroha-release.rpm
dnf -y install xsos rsar
dnf -y install sysstat
```
See https://github.com/ryran/xsos/ for usage

See https://github.com/ryran/rsar for rsar usage

## Converting and analyzing sar data
```
sadf -c sa28 > sa28.new # converts older binary sar28 format to installed sar format 
sar -d -f sal4.new|sort -n -k +9 # sort sar output by the 9th column
sar -S -f sal4.new # view swap information
```

## Linux boot process
### RHEL 
#### grub setting for dracut shell
change ```rd.shell=0``` to ```rd.break``` to drop into dracut shell on boot

## Transciever Speeds and Feeds

| Module   | Ethernet Speed|
|----------|-------------|
| SFP | 4 Gbits/s |
| SFP+ | 10 Gbits/s |
| SFP28 | 25 Gbits/s |
| QSFP+ | 40Gbits/s or 4 x 10 Gbits/s |
| QSFP28 | 100Gbits/s or 4 x 25 Gbits/s |
| QSFP56 | 200Gbits/s | 
| QSFP-DD | 400 Gbits/s or 8 x 50 Gbits/s| 

| Fibre Channel (FC) SFP+ Modules| FC Speed |
|----------|-------------|
| FC SFP+ | 8G |
| FC SFP+ | 16G |
| FC SFP+ | 32G |
| FC SFP+ | 64G |

## ping an IP Range
```
#!/bin/bash
PREFIX=192.168.86
for IP in $(seq 1 254)
do 
	ping -c 1 -t 1 $PREFIX.$IP > /dev/null
	[ $? -eq 0 ] && echo "$PREFIX.$IP UP" || : 
done
```
## OS X
```
# Interface information including DHCP
ipconfig getpacket [interface]
```   
