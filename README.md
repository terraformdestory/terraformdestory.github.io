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
```sh
glcoud auth login

gcloud auth list

gcloud config set accessibility/screen_reader false

gcloud config set project [project]

gcloud config set compute/region [region]

gcloud compute project-info add-metadata --metadata google-compute-default-region=[region]

gcloud config configurations list

gcloud sql instances list

gcloud sql instances describe [instance_name]
```
### Deploy a Compute Engine VM
```
#!/bin/bash

VM_PREFIX=$1
IMAGE_FAMILY=ubuntu-2204-lts
RND_STRING=$(echo $RANDOM|md5sum|cut -c1-4)
GCP_ZONE=us-central1-b
VM_NAME=$VM_PREFIX-$IMAGE_FAMILY-$RND_STRING
IMAGE_PROJECT=ubuntu-os-cloud

# Deploy Compute Engine VM instance
gcloud compute instances create $VM_NAME \
        --image-project=$IMAGE_PROJECT \
        --image-family=$IMAGE_FAMILY \
        --boot-disk-type=pd-standard \
        --machine-type=e2-micro \
        --zone=$GCP_ZONE
```

## tmux
crtl + b [command] (or : for tmux command prompt)

ctrl + b, d (detach)

tmux ls

tmux a -t [session] (attach to a specific session)

#### Managing Panes
ctrl + b, " (split horizontal)

ctrl + b, % (split vertically)

ctrl + b, [arrow] (navigate panes)

ctrl + b, :, setw synchronize-panes on

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
machine github.com login [username] password [personal access token]

git stash to resolve push conflicts
```
git stash # caches local changes
git pull # syncs from origin
git stash pop # restores local changes
```

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
```
