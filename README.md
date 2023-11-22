# threescale-gitops-example

## Prerequisites
* Red Hat Integration 3Scale operator is installed
* Red Hat OpenShift GitOps operator is installed
* OpenShift GitOps is configured with the correct permissions to make changes to the target namespaces

## Installation Instructions
1. Run this command:
```
oc create -f argocd/
```

## What happens when you run the install command
1. The oc command will create an ClusterRoleBinding that authorizes OpenShift GitOps to make changes to your cluster (i.e. give it the cluster-admin role)
2. The oc command will create an ApplicationSet that GitOps will use to create some applications
3. ArgoCD will see the ApplicationSet and create two applications in the openshift-gitops namespace: 3scale-dev and 3scale-test
4. ArgoCD will see the applications, and clone this repository. It will do this twice, once for each application
5. ArgoCD will run kustomize to create the ApiManager objects. It will do the equivalent to running `oc create -k overlays/dev/` and `oc create -k overlays/test/`. Kustomize will interpret files like overlays/test/set-wildcard-domain.yaml which will make the two ApiManager instances look slightly different.
7. The 3Scale operator will see the ApiManager objects and create the DeploymentConfigs, Routes and other resources that make up 3Scale.

## Notes
* Reference for kustomize.yaml and set-wildcard-domain.yaml: https://kubernetes.io/docs/tasks/manage-kubernetes-objects/kustomization/.
* OpenShift GitOps uses the equivalent `kubectl kustomize` internally.

