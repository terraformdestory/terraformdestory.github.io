# Misc tech notes

## OpenShift
```sh
oc project [project_name]

oc get pods

oc status

oc logs -f [podname]
```

## Google Cloud
```sh
glcoud auth login

gcloud auth list

gcloud config set project [project]

gcloud config set compute/region [region]

gcloud compute project-info add-metadata --metadata google-compute-default-region=[region]

gcloud config configurations list
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
#### Example Container Build
```sh
git clone https://github.com/spkane/docker-node-hello.git \
	--config core.autocrlf=input
    
docker build -t example/docker-node-hello:latest .

docker run --rm -ti 8a773166616c /bin/bash #Run a bash shell in a specific container layer
```
#### Run the Container Created in the Previous Steps
```sh
docker run -d -p 8080:8080 example/docker-node-hello:latest
```

#### Show running containers
```sh
docker ps
```

#### Stop a container
```sh
docker stop b7145e06083f
```
