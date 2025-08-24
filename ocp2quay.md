## Push images from Internal OCP repository to Quay
``` sh
oc get nodes
oc debug nodes/control-plane-cluster-hqrxp-1
chroot /host

oc login --token=<> --server=https://api.cluster-hqrxp.dynamic.redhatworkshops.io:6443

HOST=$(oc get route default-route -n openshift-image-registry --template='{{ .spec.host }}')
podman login -u kubeadmin -p $(oc whoami -t) $HOST

oc project demo1

oc get is 

podman pull default-route-openshift-image-registry.apps.cluster-hqrxp.dynamic.redhatworkshops.io/demo1/parksmap
podman pull default-route-openshift-image-registry.apps.cluster-hqrxp.dynamic.redhatworkshops.io/demo1/nationalparks
podman pull default-route-openshift-image-registry.apps.cluster-hqrxp.dynamic.redhatworkshops.io/demo1/mongodb:6.0.4

podman tag default-route-openshift-image-registry.apps.cluster-hqrxp.dynamic.redhatworkshops.io/demo1/parksmap quay.io/arslankhanali/parksmap:frontend
podman tag default-route-openshift-image-registry.apps.cluster-hqrxp.dynamic.redhatworkshops.io/demo1/nationalparks quay.io/arslankhanali/parksmap:backend
podman tag default-route-openshift-image-registry.apps.cluster-hqrxp.dynamic.redhatworkshops.io/demo1/mongodb:6.0.4  quay.io/arslankhanali/parksmap:db


podman login quay.io -u arslankhanali -p <>

podman push quay.io/arslankhanali/parksmap:frontend
podman push quay.io/arslankhanali/parksmap:backend
podman push quay.io/arslankhanali/parksmap:db

```