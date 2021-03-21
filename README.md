# Openshift Cluster Config
Setting up an OpenShift cluster using Kustomize and ArgoCD. More info coming soon.

HEAVILY borrowed from [the Red Hat Canadia team's repo](https://github.com/redhat-canada-gitops/cluster-config) :canada:


## Installing ArgoCD

> :warning: This is based on the argocd community operator using an "Automatic" update strategy on OpenShift 4.7 deploying ArgoCD 1.8.2

To install argocd using the operator, use this repo.

```
until oc apply -k https://github.com/christianh814/openshift-cluster-config/argocd/install; do sleep 2; done
```

This will start the installation of argocd. You can monitor the install with a `watch` on the following command.

```
oc get pods -n argocd
```

To get your argocd route (where you can login)

```
oc get route argocd-server -n argocd -o jsonpath='{.spec.host}{"\n"}'
```

## Deploying this Repo

To configure your cluster to this repo run

```
oc apply -k https://github.com/christianh814/openshift-cluster-config/cluster-config/config/overlays/default
```

This will configure your server with the following.

Cluster Configurations:
* HTPassword Authentication
  * Three users: `ocp-admin`,`ocp-developer`, and `ocp-marketing`
* Three Groups created
  * `admins`
    * `ocp-admin` is part of `admins`
  * `developer`
    * `ocp-developer` is part of `developer`
  * `marketing`
    * `ocp-marketing` is part of `marketing`
* ClusterRole/Role Bindings setup
  * `admins` group has `cluster-admin` on OpenShift
  * The `developer` group has `edit` on the `pricelist` namespace on OpenShift
* Container Security Operator installed
* Pipelines Operator installed
* Sealed Secrets installed

Application Deployments:
* Deploy Pricelist in an ArgoCD project called `pricelist`
  * One `application` Consisting of...
    * Frontend Web Application
    * Backend Database store
    * Job that creates database tables and the such
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
