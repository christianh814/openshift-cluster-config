# Openshift Cluster Config
Setting up an OpenShift cluster using Kustomize and ArgoCD. More info coming soon.

HEAVILY borrowed from [the Red Hat Canadia team's repo](https://github.com/redhat-canada-gitops/cluster-config) :canada:


## Installing ArgoCD

# Create namespace

oc create -f 1.argocd-namespace.yaml

# Switch to namespace

oc project argocd

# Create Operator Group

oc create -f 2.argocd-operatorgroup.yaml

# Create Subscription

oc create -f 3.argocd-subscription.yaml

# Approve the subscription

oc patch -n argocd installplan  $(oc get installplan -n argocd -o jsonpath='{.items[0].metadata.name}') --type=json \
-p='[{"op":"replace","path": "/spec/approved", "value": true}]'

# Create ArgoCD instance

oc create -f ../argocd.yaml


1. Install ArgoCD operator
2. `oc apply -f https://raw.githubusercontent.com/christianh814/openshift-cluster-config/master/argocd.yaml` in this repo
3. Assumes you have a `storageClass` named `gp2-storage` with a binding mode of `Immediate`
4. Run `oc apply -k https://github.com/christianh814/openshift-cluster-config/cluster-config/config/overlays/default`











VVVVVVV Delete? VVVVVVVVV
__Gotchas__

Not sure why, but had to run the following as step `3b`

```
oc adm policy add-cluster-role-to-user cluster-admin system:serviceaccount:argocd:argocd-application-controller
```

^ need to investigate more
