# Openshift Cluster Config
Setting up an OpenShift cluster using Kustomize and ArgoCD. More info coming soon.

HEAVILY borrowed from [the Red Hat Canadia team's repo](https://github.com/redhat-canada-gitops/cluster-config) :canada:

1. Install ArgoCD operator
2. `oc apply -f https://raw.githubusercontent.com/christianh814/openshift-cluster-config/master/argocd.yaml` in this repo
3. Assumes you have a `storageClass` named `gp2-storage` with a binding mode of `Immediate`
4. Run `oc apply -k https://github.com/christianh814/openshift-cluster-config/cluster-config/config/overlays/default`

__Gotchas__

Not sure why, but had to run the following as step `3b`

```
oc adm policy add-cluster-role-to-user cluster-admin system:serviceaccount:argocd:argocd-application-controller
```

^ need to investigate more
