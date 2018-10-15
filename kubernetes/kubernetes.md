# Kubernetes Notes

## General notes

### Core concepts

![High Level
Concepts](https://github.com/leoyu037/cheatsheets/blob/master/kubernetes/.resources/kubernetes-high-level-concepts.png)

- Node types:
    - Master: monitors/controls node state
    - Node: runs deployed containers
    - Bastion: hardened node that serves as a gateway to the rest of the cluster
- Kubernetes objects
    - Low level objects:
        - [Namespace](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/):
          a 'virtual cluster' (k8s supports multiple virtual clusters backed by the
          same physic cluster)
            - Names of resources are unique within a namespace, but not across
              namespaces
        - [Pod](https://kubernetes.io/docs/tutorials/kubernetes-basics/explore/explore-intro/):
          group of one or more application containers and shared resources,
          basic unit for all workloads
            - Represents a "logical host" where the application containers are
              relatively tightly coupled
            - Containers in a pod share an IP and port space and can find each
              other via `localhost`
            - All containers in a pod always co-located on the same node,
              co-scheduled, and share the same life cycle
            - In most cases, pods should be created with controllers rather than
              created directly
            - [More in-depth reference](https://kubernetes.io/docs/concepts/workloads/pods/pod/)
        - [Service](https://kubernetes.io/docs/concepts/services-networking/service/):
          defines a logical set of pods and a policy by which to access them
            - Every service gets its own `cluster IP`, which is shared by every
              pod in the service
            - Services continuously use selectors to determine which pods to
              apply its rules to
            - Used to abstract pods, but can also be used to abstract external
              services (see docs for how to do this)
        - Volume: storage that is tied to the lifecycle of a pod
        - PersistentVolume: storage at the cluster level that applications can
          use
    - Controllers (high level objects):
        - [ReplicaSet](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/):
          ensures that a specified number of pod replicas are running
            - Recommended to use deployments, which manage replicasets and more
        - [Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/):
          provides declarative updates for pods and replicasets
            - Has builtin support for rolling deployments, but blue/green and
              canary deployments have to be custom-implemented using multiple
              deployment objects
        - StatefulSet:
        - [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/):
          ensures that all nodes run a copy of a pod
            - Daemonset pod lifecycles are tied to node lifecycles
            - Used for pods that provide machine-level functions like machine
              monitoring and logging
        - [Job](https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/):
    - Configuration:
        - [ConfigMap](https://github.com/leoyu037/cheatsheets/blob/master/kubernetes/configmap.md): used to store non-confidential data (e.g. nginx config), can
          be mounted as a file or as an env var
        - Secret: used to store confidential data (e.g. database creds), can be
          mounted as a file or as an env var
    - [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/):
      API object managing external access (typically http) to the services in a
      cluster
    - Addons: pods/services that implement cluster features

### Kube architecture

- Master:
    - kube-api-server: http frontend for the cluster
    - kube-controller-manager: runs core cluster processes (some examples below)
        - Node Controller: notices and responds to nodes going down
        - Replication Controller: maintains correct # of pods
        - Route Controller, Service Controller, Volume Controller, etc.
    - kube-scheduler: figures out where to start new pods
    - etcd: key-value store holding all cluster data
- Node:
    - kubelet: makes sure containers are running in a pod
    - kube-proxy: manages networking for the node

### Object metadata

- [Labels](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/):
  used to tag and select/filter on objects
    - Can filter and retrieve k8s objects via command line
        - ex. `kubectl get pods -l 'environment notin (qa, production),tier=frontend'`
    - Other k8s objects also use selectors (e.g. services)
- [Annotations](https://kubernetes.io/docs/concepts/overview/working-with-objects/annotations/):
  used to attach arbitrary metadata to k8s objects, annotations aren't
  filterable

### Service discovery/DNS

If the kube-dns addon is installed, it will continuously watch for new services
and create DNS entries for them. For example, if you have a service called
`my-service` in Kubernetes Namespace `my-ns` a DNS record for
`my-service.my-ns` is created. Pods which exist in the `my-ns` Namespace should
be able to find it by simply doing a name lookup for `my-service`. Pods which
exist in other Namespaces must qualify the name as `my-service.my-ns`. The
result of these name lookups is the cluster IP.

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

## `ksonnet1 (sucks)

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
    - `ks env set {env} --name {name}`
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
