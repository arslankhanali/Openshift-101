## Install OpenShift gitops operator
```sh
oc apply -k https://github.com/redhat-cop/gitops-catalog/openshift-gitops-operator/operator/overlays/latest

oc apply -k https://github.com/redhat-cop/gitops-catalog/openshift-gitops-operator/instance/overlays/default
```
## Deploy argoapp
It will create a namespace named demo-gitops
```sh
oc new-project demo-gitops
oc project demo-gitops

oc apply -f argocd/argo_app.yaml
```

### Make a Databse in mongo
```sh
oc project demo-gitops
# Create a Database in mongo
POD=$(oc get pod -l role=database -o jsonpath='{.items[0].metadata.name}')
oc exec -it $POD -- \
  mongosh -u admin -p secret --authenticationDatabase admin \
  --eval 'use parksapp' \
  --eval 'db.createUser({user: "parksapp", pwd: "keepsafe", roles: [{ role: "dbAdmin", db: "parksapp" }, { role: "readWrite", db: "parksapp" }]})' \
  --quiet
```

### Push data to mongo
``` sh
# Load the data into mongo
curl "https://$(oc get route nationalparks -o jsonpath='{.spec.host}' | tr -d '\n')/ws/data/load"
```

### See the map
```sh
# Get the URL to the map - you should see the parks now
echo "https://$(oc get route parksmap -o jsonpath='{.spec.host}')"
```

### Delete all
```sh
oc delete -f argocd/argo_app.yaml
oc delete all --all -n demo-gitops
oc delete project demo-gitops 
```

---
# ------------------------------------------------------------------------
### Login
```sh
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d

argocd login <> --insecure --username admin \
  --password $(kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d)
```


### Errors: Permission issues in argo
``` sh
oc get argocd openshift-gitops -n openshift-gitops -o yaml | oc neat

# Make sure it has 

rbac:
    defaultPolicy: ""
    policy: |
      g, admin, role:admin

or

rbac:
   - defaultPolicy: 'role:admin'
```


### Argo Controller crashing - OOM error
By default has only 2Gi memory limit.
Increate the memory limit in yaml for argoCD.
Intalled Operator -> Openshift gitops -> Argo CD -> `name:openshift-gitops king:ArgoCD ` -> edit yaml 


### Make argo manifests
```sh
oc login --token=<> --server=https://api.cluster-hqrxp.dynamic.redhatworkshops.io:6443
oc get all -n <your-namespace> -o yaml > namespace-resources.yaml

```