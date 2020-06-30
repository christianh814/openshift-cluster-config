# Openshift Cluster Config
Setting up an OpenShift cluster using Kustomize and ArgoCD. More info coming soon.

HEAVILY borrowed from [the Red Hat Canadia team's repo](https://github.com/redhat-canada-gitops/cluster-config) :canada:


## Installing ArgoCD

> :warning: This is based on the argocd operator v0.9 ...[Andrew](https://github.com/pittar) says there's breaking changes coming in v0.10

You first need to install ArgoCD using the Operator, first create the namespace

```
oc create -f https://raw.githubusercontent.com/christianh814/openshift-cluster-config/master/argocd/1.argocd-namespace.yaml
```

Next, make sure you're on that namespace

```
oc project argocd
```

Next, create the Operator Group

```
oc create -f https://raw.githubusercontent.com/christianh814/openshift-cluster-config/master/argocd/2.argocd-operatorgroup.yaml
```

Now, create  the subscription

```
oc create -f https://raw.githubusercontent.com/christianh814/openshift-cluster-config/master/argocd/3.argocd-subscription.yaml
```

The subscription is set to a "manual" approval of updates. For whatever reason, OLM thinks that install is the same as an update? Anyway; manyally approve the "update" (or what's actually happening; an install)

```
oc patch -n argocd installplan  $(oc get installplan -n argocd -o jsonpath='{.items[0].metadata.name}') --type=json \
-p='[{"op":"replace","path": "/spec/approved", "value": true}]'
```

Now you can create your ArgoCD instance

```
oc create -f https://raw.githubusercontent.com/christianh814/openshift-cluster-config/master/argocd/4.argocd-instance.yaml
```

## Deploying this Repo

To configure your cluster to this repo run

```
oc apply -k https://github.com/christianh814/openshift-cluster-config/cluster-config/config/overlays/default
```

This will configure your server with the following.

Cluster Configurations:
* HTPassword Authentication
* Install Container Security Operator

Application Deployments:
* Deploy Pricelist in a project called `pricelist`
  * One `application` running the frontend
  * Another `application` running the database
  * The manifests for this app lives in my [gitops example repo](https://github.com/christianh814/gitops-examples)


# How do I make changes

You don't, it's GitOps!

Jokes aside, the idea is to manage your cluster by pull request to the right repo. In a lot of instances, that means many PRs to may repos!











VVVVVVV Delete? VVVVVVVVV
__Gotchas__

Not sure why, but had to run the following as step `3b`

```
oc adm policy add-cluster-role-to-user cluster-admin system:serviceaccount:argocd:argocd-application-controller
```

^ need to investigate more
