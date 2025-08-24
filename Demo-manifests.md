# Applying manifest method

### Create a namespace
`oc project demo-manifest`

### Aply manifests
```sh
oc apply -f manifests/1-frontend.yaml
oc apply -f manifests/2-secret.yaml
oc apply -f manifests/3-backend.yaml
oc apply -f manifests/4-database.yaml
```
### Make a Databse in mongo
```sh
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
# See if the route works
curl "https://$(oc get route nationalparks -o jsonpath='{.spec.host}' | tr -d '\n')/ws/info"

# See if there is any data
curl "https://$(oc get route nationalparks -o jsonpath='{.spec.host}' | tr -d '\n')/ws/data/all"

# Load the data into mongo
curl "https://$(oc get route nationalparks -o jsonpath='{.spec.host}' | tr -d '\n')/ws/data/load"
```
### See the map
```sh
# Get the URL to the map - you should see the parks now
curl https://$(oc get route parksmap -o jsonpath='{.spec.host}')
```

## Delete all
``` sh
oc delete all --all -n demo-manifest
oc delete project demo-manifest
```