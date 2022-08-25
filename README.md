# Misc tech notes

## OpenShift
```sh
oc project [project_name]

oc get pods

oc status

oc logs -f [podname]

oc get claim --all-namespaces
```

## Google Cloud
```sh
glcoud auth login

gcloud auth list

gcloud config set project [project]

gcloud config set compute/region [region]

gcloud compute project-info add-metadata --metadata google-compute-default-region=[region]

gcloud config configurations list

gcloud sql instances list

gcloud sql instances describe [instance_name]
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

ctrl + b, :, setw syncrhonize-panes on

## Kubernetes
#### K8s The Hardway Tutorial
https://github.com/kelseyhightower/kubernetes-the-hard-way
#### Deploying the Kubernetes Dashboard
https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/
#### Creating an admin user for the dashboard
https://github.com/kubernetes/dashboard/blob/master/docs/user/access-control/creating-sample-user.md

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
VALUE=$(echo $RANDOM | md5 | head -c 20)
vault kv patch some/vaultpath/secrets token="$VALUE" # set the key "token" and the value "$VALUE" under the path some/vaultpath/secrets
```
