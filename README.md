# Openshift Cluster Config
Setting up an OpenShift cluster using Kustomize and ArgoCD. More info coming soon.

HEAVILY borrowed from [the Red Hat Canadia team's repo](https://github.com/redhat-canada-gitops/cluster-config) :canada:


## Installing ArgoCD

> :warning: This is based on the argocd operator v0.9 using an "Automatic" update strategy ...[Andrew](https://github.com/pittar) says there's breaking changes coming in v0.10 - so be warned!

To install argocd using the operator, use this repo.

```
oc apply -k https://github.com/christianh814/openshift-cluster-config/argocd/install
```

__NOTE__

You may get the following error...

```
error: unable to recognize "https://github.com/christianh814/openshift-cluster-config/argocd/install": no matches for kind "ArgoCD" in version "argoproj.io/v1alpha1"
```

That's okay, just run the `oc apply -k` command again.


## Deploying this Repo

To configure your cluster to this repo run

```
oc apply -k https://github.com/christianh814/openshift-cluster-config/cluster-config/config/overlays/default
```

This will configure your server with the following.

Cluster Configurations:
* HTPassword Authentication
  * Two users: `ocp-admin` and `ocp-developer`
* Two Groups created
  * `admins`
    * `ocp-admin` is part of `admins`
  * `developer`
    * `ocp-developer` is part of `developer`
* ClusterRole/Role Bindings setup
  * `admins` group has `cluster-admin` on OpenShift
  * The `developer` group has `edit` on the `pricelist` namespace on OpenShift
* Container Security Operator installed

Application Deployments:
* Deploy Pricelist in an ArgoCD project called `pricelist`
  * One `application` running the frontend
  * Another `application` running the database
  * The 3rd `application` just creates the namespace called `pricelist`
  * The manifests for this app lives in my [gitops example repo](https://github.com/christianh814/gitops-examples)

ArgoCD Configurations
* ArgoCD is integrated with the OpenShift oAuth
* RBAC Policy
  * The `admins` OpenShift group is set up as ArgoCD admins
  * The `developer` OpenShift group is set up as ArgoCD users
  * ArgoCD admins can see and sync all ArgoCD Applications
* The `cluster-config` ArgoCD project has all "cluster wide" configurations
  * Can only be seen/synced by ArgoCD admins
* The `pricelist` ArgoCD project has all appliaction components to run the [Pricelist](https://github.com/christianh814/openshift-cluster-config) application
  * Can be seen/synced by ArgoCD admins or ArgoCD users
* Autosync is turned on

# How do I make changes

You don't, it's GitOps!

Jokes aside, the idea is to manage your cluster by pull request to the right repo. In a lot of instances, that means many PRs to many repos!
