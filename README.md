# Misc tech notes

## OpenShift
oc project [project_name]

oc get pods

oc status

oc logs -f [podname]


## Google Cloud
glcoud auth login

gcloud auth list

gcloud config set project [project]

gcloud config set compute/region [region]

gcloud compute project-info add-metadata --metadata google-compute-default-region=[region]

gcloud config configurations list

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
