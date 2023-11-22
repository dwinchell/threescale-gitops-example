# threescale-gitops-example

## Prerequisites
* Red Hat Integration 3Scale operator is installed
* Red Hat OpenShift GitOps operator is installed
* OpenShift GitOps is configured with the correct permissions to make changes to the target namespaces

## Installation Instructions

1. Either clone this entire repo into your enterprise Git server, or copy whichever files you want into an existing repository. You will need the files under argocd/ base/ and overlays/.
2. Update argocd/applicationset.yaml to change the repoURL field to point at your own git repository. Use the same URL that you would use for `git clone`.
3. If your Git server requires authentication: open the ArgoCD console, go to Settings -> Connect Repo, and fill in your server's information.
5. Clone the repository locally using `git clone` so that you can run the install command.
6. Inside of the cloned directory, run the install command: `oc create -f argocd/`

## What happens when you run the install command
1. The oc command will create an ClusterRoleBinding that authorizes OpenShift GitOps to make changes to your cluster (i.e. give it the cluster-admin role)
2. The oc command will create an ApplicationSet that GitOps will use to create some applications
3. ArgoCD will see the ApplicationSet and create two Applications in the openshift-gitops namespace: 3scale-dev and 3scale-test
4. ArgoCD will see the Applications, and clone this repository. It will do this twice, once for each Application
5. ArgoCD will run kustomize on the directories specified in each Application. This will be equivalent to running `oc create -k overlays/dev/` and `oc create -k overlays/test/`.
6. Kustomize will use files under overlays/ and base/ to create two ApiManager objects. It will create them with slightly different options because it will process files like overlays/dev/set-wildcard-domain.yaml and overlays/test/set-wildcard-domain.yaml.
7. The 3Scale operator will see the ApiManager objects and create the DeploymentConfigs, Routes and other resources that make up 3Scale.

## Notes
* Reference for kustomize.yaml and set-wildcard-domain.yaml: https://kubernetes.io/docs/tasks/manage-kubernetes-objects/kustomization/.
* OpenShift GitOps uses the equivalent `kubectl kustomize` internally.
