## Install Pipeline operator
```sh
oc apply -k https://github.com/redhat-cop/gitops-catalog/openshift-pipelines-operator/overlays/latest
```
## Create a project
``` sh
oc new-project demo-pipeline
oc project demo-pipeline
```

## `Buildlah` and `s2i` tasks need privileged access 
```sh
oc adm policy add-scc-to-user privileged -z pipeline -n demo-pipeline
#or
oc apply -f tekton/rb.yaml
```

## Create QUAY secret, if you want to push to QUAY
``` sh
# oc apply -f tekton/.secret.yaml
oc apply -f tekton/secret.yaml

# oc patch sa pipeline -n demo-pipeline --type='json' -p='[ {"op": "add", "path": "/secrets/-", "value":{"name":"quay-pass"}} ]'

oc get sa pipeline -o yaml | oc neat
```

## Install tasks 
``` sh
oc get task
kubectl apply -f https://raw.githubusercontent.com/tektoncd/catalog/main/task/git-clone/0.6/git-clone.yaml
kubectl apply -f https://raw.githubusercontent.com/tektoncd/catalog/main/task/maven/0.2/maven.yaml
kubectl apply -f https://raw.githubusercontent.com/tektoncd/catalog/main/task/buildah/0.3/buildah.yaml
kubectl apply -f https://raw.githubusercontent.com/tektoncd/catalog/main/task/openshift-client/0.2/openshift-client.yaml

oc get task 
```

## Create a pipeline and start run
Each pipeline needs a PVC which will be created
```sh
oc project demo-pipeline
oc apply -f tekton/pipeline-backend.yaml
oc apply -f tekton/pipeline-frontend.yaml
```

## Manually start pipeline
1. Run Pipeline
   1. Under Workspaces
      1. App-source: 
         1. PersistentVolumeClaim
         2. pvc-parksmap
   2. Start


### Delete
``` sh
oc delete pipeline --all -n demo-pipeline && \   
oc delete pipelineruns --all -n demo-pipeline && \
oc delete pvc --all -n demo-pipeline 

oc delete secret quay-pass -n demo-pipeline

oc delete project demo-pipeline

# oc patch sa pipeline -n demo-pipeline --type='json' -p='[ {"op": "remove", "path": "/secrets/0"} ]'
```