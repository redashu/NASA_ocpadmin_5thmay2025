# Advanced Scheduling in OpenShift (and Kubernetes)

In OpenShift (and Kubernetes), node selectors, affinity, and anti-affinity are advanced features used to control the placement of pods on specific nodes within a cluster. These tools provide mechanisms for fine-tuning where pods should be scheduled based on node characteristics or other factors. Let’s break down each concept in detail.

## 1. Node Selectors

A node selector is the simplest form of node placement in OpenShift. It allows you to specify that a pod should run only on nodes that have specific labels. Labels are key-value pairs that you assign to nodes in the cluster, and node selectors use these labels to determine the node selection criteria.

### Syntax

In a pod specification, the `nodeSelector` field is used to define the labels that must be present on the node.

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: mypod
spec:
    nodeSelector:
        disktype: ssd
    containers:
    - name: mycontainer
        image: myimage
```

In this example, the pod will only be scheduled on nodes that have a label `disktype=ssd`. If no node matches this criterion, the pod won't be scheduled.

### Key Points

- **Simple**: It’s a simple way to target specific nodes.
- **No Tolerance or Preference**: The pod must be scheduled on a node with matching labels; otherwise, it will remain unscheduled.
- **Limited Flexibility**: Unlike affinity and anti-affinity rules, node selectors cannot express complex scheduling logic (like expressing "preferred" nodes).

## 2. Affinity and Anti-Affinity

Both affinity and anti-affinity are more advanced mechanisms than node selectors for pod scheduling. These concepts allow you to specify rules for pod placement based on node labels or other pods' labels.

### Affinity

Affinity allows you to specify that a pod should be scheduled in a particular way, relative to other pods or based on node characteristics. It can be further categorized into **node affinity** and **pod affinity**.

#### Node Affinity

Node affinity allows you to constrain which nodes a pod can be scheduled on, based on node labels. It is more flexible than node selectors and supports preferred and required rules.

#### Syntax

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: mypod
spec:
    affinity:
        nodeAffinity:
            requiredDuringSchedulingIgnoredDuringExecution:
                nodeSelectorTerms:
                - matchExpressions:
                    - key: disktype
                        operator: In
                        values:
                        - ssd
            preferredDuringSchedulingIgnoredDuringExecution:
                - weight: 1
                    preference:
                        matchExpressions:
                        - key: region
                            operator: In
                            values:
                            - us-west
    containers:
    - name: mycontainer
        image: myimage
```

In this example:

- `requiredDuringSchedulingIgnoredDuringExecution`: The pod must be scheduled on nodes that have a `disktype=ssd` label.
- `preferredDuringSchedulingIgnoredDuringExecution`: The pod prefers to run on nodes in the `us-west` region, but if no suitable node is found, it will still be scheduled elsewhere (if possible).

#### Pod Affinity

Pod affinity lets you schedule pods based on the labels of other pods. For example, you may want certain pods to be scheduled together (e.g., for latency optimization or co-location of components).

#### Syntax

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: mypod
spec:
    affinity:
        podAffinity:
            requiredDuringSchedulingIgnoredDuringExecution:
                labelSelector:
                    matchExpressions:
                        - key: app
                            operator: In
                            values:
                                - frontend
                topologyKey: "kubernetes.io/hostname"
    containers:
    - name: mycontainer
        image: myimage
```

In this example:

- `requiredDuringSchedulingIgnoredDuringExecution`: The pod will only be scheduled on nodes that already have a pod with the label `app=frontend`.
- `topologyKey`: This key (usually `kubernetes.io/hostname`) specifies that the affinity rule applies within the same node (i.e., the pod must be scheduled on the same node as another pod with the specified label).

### Pod Anti-Affinity

Pod anti-affinity, conversely, prevents pods from being scheduled together on the same node. This can be useful when you want to spread certain pods across different nodes for high availability or fault tolerance.

#### Syntax

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: mypod
spec:
    affinity:
        podAntiAffinity:
            requiredDuringSchedulingIgnoredDuringExecution:
                labelSelector:
                    matchExpressions:
                        - key: app
                            operator: In
                            values:
                                - database
                topologyKey: "kubernetes.io/hostname"
    containers:
    - name: mycontainer
        image: myimage
```

In this example:

- `requiredDuringSchedulingIgnoredDuringExecution`: This rule prevents the pod from being scheduled on nodes that already have a pod with the label `app=database`.
Pod Affinity is used to co-locate pods based on other pods' labels.

Pod Anti-Affinity is used to spread pods across nodes, ensuring they do not run together.

Both affinity and anti-affinity can have required and preferred rules, providing a higher degree of flexibility than node selectors.

3. Affinity and Anti-Affinity Rules: Required vs. Preferred
Required Rules: These are mandatory rules. If no nodes satisfy the required rules, the pod will not be scheduled.

Preferred Rules: These are optional rules. If nodes that satisfy the preferred rules are available, the scheduler will prefer those, but it will still schedule the pod on another node if necessary.

Practical Use Cases:
Node Affinity:

Assigning workloads to nodes with specific hardware (e.g., GPUs or SSDs).

Ensuring certain workloads only run in specific regions or availability zones.

Pod Affinity:

Co-locating services that need to communicate frequently with each other (e.g., microservices).

Ensuring that front-end and back-end components of an application run on the same node.

Pod Anti-Affinity:

Spreading replicas of an application across multiple nodes to ensure high availability.

Avoiding placing all instances of a stateful application on the same node.

4. Topological Domains (Topology Keys)
In both pod affinity and anti-affinity, the topologyKey determines the domain within which the affinity or anti-affinity rules apply. Common examples include:

kubernetes.io/hostname: Ensures that the pod is scheduled on the same or different node.

failure-domain.beta.kubernetes.io/zone: Ensures that the pod is scheduled in the same or different availability zone.

topology.kubernetes.io/region: Ensures the pod is scheduled in the same or different region.

Conclusion
Node selectors provide simple node-based scheduling, while affinity and anti-affinity offer more complex scheduling based on pod relationships and node labels.

These features enhance scheduling flexibility, ensuring your workloads are placed effectively based on your needs, whether it's for hardware requirements, high availability, or fault tolerance.