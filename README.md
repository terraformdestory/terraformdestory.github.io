# Misc tech notes

## OpenShift

### Set OpenShift project
oc project [project_name]

### Show pods
oc get pods

### Show an overview of the current project
oc status

### Follow logs for a given pod
oc logs -f [podname]


## Google Cloud
glcoud auth login
gcloud auth list
gcloud config set project [project]
gcloud compute project-info add-metadata --metadata google-compute-default-region=[region]

