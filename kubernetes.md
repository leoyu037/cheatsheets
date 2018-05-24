# Kubernetes Notes

## General notes

### Core concepts

- Node types:
    - Master:
    - Bastion: hardened node that serves as a gateway to the rest of the cluster

- Namespace:
- Deployment:
- Pod:
- Replica set:
- Service:

--------------------------------------------------------------------------------

## `kubectl`

Tool for administering a kubernetes cluster

### Core concepts

- kubeconfig: kubectl configuration, specifies the clusters/contexts/users that
  kubectl will have access to (I think kops also uses it). `$KUBECONFIG` env
  var specifies which one kubectl will use
- Context: cluster/namespace/user combo to work with, can switch contexts to
  manage multiple clusters/namespaces
- Manifest: json/yaml files describing resources/operations to create/apply to
  a cluster

### Common operations

- List nodes: `kubectl get nodes --show-labels`
- Create a new resource: `kubectl create {resource type} name`
    - ex. `kubectl create namespace my-namespace`
- Modify resources from manifest: `kubectl create/apply -f {some kube manifest}`
    - See [create vs. apply](https://kubernetes.io/docs/concepts/overview/object-management-kubectl/overview/)

- `kubectl delete namespace {namespace}`
- `kubectl config delete-context {context}`

--------------------------------------------------------------------------------

## `kops`

Tool for provisioning/managing a kubernetes cluster

### Common operations

- Create kops manifest: `kops create cluster {options} --dry-run -o yaml > {some yaml file}`
- Create cluster config out of manifest: `kops create -f {kops manifest yaml}`
- Deploy cluster: `kops update cluster {cluster name}`
- Create secret: `kops create secret --name {cluster name} sshpublic admin -i {public key path}`
- Edit cluster config: `kops edit cluster {cluster name}`
- Delete cluster: `kops delete -f {kops manifest yaml}`
- Validate cluster: `kops validate cluster`

--------------------------------------------------------------------------------

## `ksonnet`

Tool for writing kubernetes manifests

### Core concepts

- Application: made up of multiple components
- Component: represents a group of kubernetes resources
- Environment: applications/components can be deployed to multiple environments
  using the same set of manifests and some environment-specific overrides

### Common operations

- `ks init {application} --context {kubectl context}`
- Environments:
    - `ks env list`
    - `ks env add {env} --context={kubectl context}`
    - `ks env set {env} --name {name}
    - `ks show {env}`
    - `ks apply {env}`
    - `ks delete {env}`
    - `ks param diff {env 1} {env 2}`
    - `ks diff remote:{env 1} remote:{env 2}`
- Components:
    - `ks generate {prototype} {component name}`
    - `ks param set {component} {param} {val} --env {env}`
- Prototypes:
    - `ks prototype list`
    - `ks prototype describe {prototype}`
    - `ks pkg list`
    - `ks pkg install {prototype}`
