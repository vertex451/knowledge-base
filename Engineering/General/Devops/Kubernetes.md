# Pre setup
```
alias k=kubectl                         
export do="--dry-run=client -o yaml"    # k create deploy nginx --image=nginx $do

export now="--force --grace-period 0"   # k delete pod x $now
```
##### Vim
The following settings will already be configured in your real exam environment in `~/.vimrc`. But it can never hurt to be able to type these down:
```
set tabstop=2
set expandtab
set shiftwidth=2
```
# Certification tips:
https://www.linkedin.com/pulse/my-ckad-exam-experience-atharva-chauthaiwale/
https://medium.com/@harioverhere/ckad-certified-kubernetes-application-developer-my-journey-3afb0901014
https://github.com/lucassha/CKAD-resources

https://medium.com/@harioverhere/ckad-certified-kubernetes-application-developer-my-journey-3afb0901014
# Cheat-sheet
- Count lines using WordCount tool:
```
kubectl get all --no-headers | wc -l
```
- `kubectl api-resources` - list all resources, its short names, namespace affiliation, etc.
- `cat /etc/kubernetes/manifests/kube-apiserver.yaml` - to get kube-apiserver config metadata:
- Access `kube-apiserver`:
```
kubectl exec -it kube-apiserver-controlplane -n kube-system -- kube-apiserver -h | grep 'enable-admission-plugins'
```
- Describe authorization.k8s.io API group:
	- `kubectl proxy 8001& `
	- `curl localhost:8001/apis/authorization.k8s.io`
- Set default namespace(ns-)
```shell
kubectl config set-context --current --namespace=NAMESPACE
```
- Grep SPARTA and show line number:
```
cat file.yaml | grep -n SPARTA
```

# Common
- Nodes -> Pods -> Containers
- Pod <- ReplicaSets <- Deployments
- yaml required fields:
	- apiVersion(v1, apps/v1)
	- kind(POD, Service, ReplicaSet, Deployment)
	- metadata
	- spec
- `kubectl apply` - creates or updates existing resource
- `kubectl create` - creates each time new resource.
- ReplicaController - ancestor of ReplicaSet.  ReplicaSet has required field `selector` which takes into an account existing pods while ensuring its amount.
- There are two ways to create resource in k8s:
	- imperative `kubectl create ns`
	- declarative `kubectl create -f definition.yaml`
- Set default namespace(ns-)
```shell
kubectl config set-context --current --namespace=NAMESPACE
```
# Args
## Docker
- `ENTRYPOINT` is a command that runs at the startup
- `CMD` is a default param

If you add params to your docker run command:
1. It be added to ENTRYPOINT command as a param.
2. It will replace CMD commands entirely
3. There is also a way to overwrite `ENTRYPOINT` by using  `--entrypoint` command

```bash
# container which starts and waits 5s
docker run ubuntu sleep 5

# ubuntu-sleeper Dockerfile
FROM ubuntu
ENTRYPOINT ["sleep"] 
CMD ["5"]

# sleep for default 5s
docker run ubuntu-sleeper 
# sleep for custom time(10s)
docker run ubuntu-sleeper 10
```

## Kubernetes
```shell
spec:
  containers:
    - name: sleeper
	  image: ubuntu-sleeper
	  command: ["sleep2"] # replaces docker's ENTRYPOINT
	  args: ["10"] # replaces docker's CMD
```
- Docker `ENTRYPOINT` can be overwritten with k8s `command` 
- Docker `CMD` command can be replaced with k8s `args
The following way is also valid:
```yaml
spec:
  containers:
  - name: ubuntu
    image: ubuntu
    command:
      - "sleep"
      - "1200"
```
# ConfigMap
The way to handle the env variables in a centralized way.
To do it you should:
1. Create a configMap
2. Reference to it in Pod template
## Create a configMap
- Imperative way `kubectl create configmap test-config`:
```
--from-file=filename - Read data directly from the file and the filename is the key

--from-file=key=filename - Read data from the file but we define the key

--from-file=directory - Read all the files in a directory and the filenames are the keys

--from-literal=key=value - Create a single key=value as a string literal
```

- Declarative way:
  instead of `spec` we have `data`:
```
  apiVersion: v1
kind: ConfigMap
metadata:
  creationTimestamp: "2023-11-20T16:05:49Z"
  name: webapp-config-map
  namespace: default
  resourceVersion: "870"
  uid: d46295c2-8218-4683-bdda-79f47bb551ea

data:
  APP_COLOR: darkblue
  APP_OTHER: disregard
```

## Reference to configMap in a Pod template
There are three ways to include config:
1. As a config file using `configMapRef`
2. As a single env var using `configMapKeyRef`
3. As volume
### configMapRef
```
spec:
  containers:
  - name: 
    image: 
    envFrom:
    - configMapRef:
	        name: app-config
```
### configMapKeyRef
```bash
...
spec:
  containers:
  - env:
    - name: APP_COLOR
      valueFrom:
        configMapKeyRef:
          name: app-config
          key: APP_COLOR
```

### configMap via volume
```
...
volumes:
- name: app-config-volume
  configMap:
    name: app-config
```
# Secrets
```shell
spec:
  containers:
  - name: envars-test-container
    image: nginx
    envFrom:
    - secretRef:
        name: test-secret
```
# Security context
Requires pod deletion
You can define it on Pod level or Container level. Container level definition overrides the Pod level.
`capabilities` can be defined only on container level.

```shell
...
spec:
  ...
  securityContext:
    runAsUser: 1000 # pod level
  containers:
	...
    securityContext:
      runAsUser: 1001 # container level
      capabilities:
        add: ["SYS_TIME"]
```

# Resources
```
spec:
  containers:
  - name: app
	...
	resources:
	  requests:
	    memory: "1G"
	    cpu: 1
	  limits:
	    memory: "2G"
	    cpu: 2
```

What if container tries to use more than allowed?
- in case of CPU  overuse, the system may throttle the container.
- in case of memory overuse, the pod will be terminated with Out Of Memory error.

In case of CPU usage `requests` and no `limits` configuration is the most optimal, in this case extra resources used by one pod can be taken by another pod due to `requests` guarantee.
## LimitRange 
Is a Namespace level object that defines the resource usage(default, min, max) for Pod
It affects only new pods which are created afterwards.
## ResourceQuota
Is a Namespace level object that defines resource usage limit to all Pods within the Namespace
# Service account
```yaml
kubectl create sa myServiceAccount
```
Get pod's  service account:
```yaml
kubectl get po -o yaml
```
To create an authorization token for SA:
```
kubectl create token dashboard-sa
```
To use SA in pod:

```yaml
...
    spec:
      serviceAccountName: dashboard-sa
      containers:
      ...
```

# Taints
Taints are a way to repel pods from certain nodes. (It tells the Node to only accept pods with certain tolerations) 
This is useful for scenarios where you want to restrict certain types of workloads from running on specific nodes, perhaps because those nodes have specialized hardware or are reserved for particular tasks.

**Important: taints don't guarantee that the pod will end up at tainted node if there are available not-tainted nodes .**
## Taint a Node
```
# kubectl taint nodes <node-name> <key>=<val>:effect

kubectl taint nodes node1 app=blue:NoSchedule
```
## Remove Taint from  the Node
- Run the same command with the dash(-) in the end with no space. 
- Or describe node, copy the full taint value and **add dash to it**:
```
kubectl taint nodes <node-name> <taintVal>- 
```
## Taint a Pod
```
...
spec:
  containers:
	...
  tolerations:
  - key: "app"
    operator: "Equal"
    value: "blue"
    effect: "NoSchedule"
```
## Taint effects
- `NoSchedule` effect prevents new pods from being scheduled onto the node unless the pod explicitly tolerates the taint.
- `PreferNoSchedule` effect is similar to `NoSchedule` but allows scheduling of pods that do not tolerate the taint. However, if there are nodes without the taint, the scheduler prefers to place the pod on those nodes.
- `NoExecute` effect not only prevents new pods from being scheduled onto the node but also evicts existing pods that do not tolerate the taint.
# Node Affinity
You can set in Pod definition Node label where pod can be scheduled:
```
...
kind: Pod
...
spec:
  ...
  nodeSelector:
    size: Large
```
But in case of complex condition, labels will not help, and here Node Affinity comes into play.
Node Affinity feature provide us an advanced capabilities to limit Pod placement.
There are two types of Node Affinity:
- `requiredDuringSchedulingIgnoredDuringExecution`: The scheduler can't schedule the Pod unless the rule is met. This functions like `nodeSelector`, but with a more expressive syntax.
- `preferredDuringSchedulingIgnoredDuringExecution`: The scheduler tries to find a node that meets the rule. If a matching node is not available, the scheduler still schedules the Pod.
**Note:** In the preceding types, `IgnoredDuringExecution` means that if the node labels change after Kubernetes schedules the Pod, the Pod continues to run(will not be evicted).
```
...
kind: Pod
...
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: topology.kubernetes.io/zone
            operator: In
            values:
            - antarctica-east1
            - antarctica-west1
```
In case of no value in label(for instance label is `justKey=`):
```yaml
spec:
  affinity:
    nodeAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: justKey
            operator: Exists
```
# Taints vs Affinity
Taints don't guarantee that the pod will end up at tainted node if there are non-tainted nodes available.
On the other hand, Affinity doesn't guarantee that non-affinity Pods will not be scheduled to the Node. 
Only combination of both can give you desired effect.
# Multi-container design patterns
Sometimes aside of application we need additional service which should share the same life cycle, storage and network. 
For this scenario we can employ multi-container Pod feature.
- Sidecar -  logging agent that collects logs and sends to the central server with no need to change application.
- Adapter - agent that can do the log normalisation before sending
- Ambassador - agent that connects to the specific database depending on the environment
# InitContainers
Process that runs only on Pod initialization.
```
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: myapp-container
	...
  initContainers:
  - name: init-myservice
    image: busybox:1.28
    command: ['sh', '-c', 'until nslookup myservice; do echo waiting for myservice; sleep 2; done;']
  - name: init-mydb
	...
```
In this case `myapp-container` will start only after successful execution of `init-myservice` and `init-mydb`, which will be run **one at a time in sequential order**.
# Readiness and Liveness probes
K8S starts sending traffic to the pod as soon as all its containers are running. But in specific cases running app doesn't mean it is ready to accept requests. Here comes `readiness` probe:
```yaml
apiVersion: v1
kind: Pod
...
spec:
  containers:
    name: simple-webapp
    ...
    readinessProbe:
      httpGet:
        path: /ready
        port: 8080
	  initialDelaySeconds: 10 # delay before start
	  periodSeconds: 5 # time interval
	  failureThreshold: 8 # default is 3
```
`livenessProbe` is used to detect hanging state of app and k8s can restart it.
# Selectors and Labels
```
# get all pods with lavels
k get pods --show-labels

# get all resources related to FE and prod
kubectl get all --selector env=prod,app=frontend
```
# Deployments
Update deployment in imperative way:
```
# update deployment's image
kubectl set image deployments/frontend simple-webapp=kodekloud/webapp-color:v2
```
## Rollout
Each deployment update creates a revision, you can check it by:
```
kubectl rollout history <deployment-name>
```
There is column called CHANGE-CAUSE, and it will be empty of during deployment update you didn't add `--record` flag.

Also, you can rollback to previous deployment by:
```
kubectl rollout undo <deployment-name>
```
Or roll back to the revision 3
```
kubectl rollout undo daemonset/abc --to-revision=3
```
In this case revision 3 will be renamed to `revision <latest-number>` and appended to the end of the rollout history.
## Strategies
### Recreate
Scales down the ReplicaSet to Zero and than back to desired number of pods. Automatically new pods will get new image.
### RollingUpdate
Creates new ReplicaSet with zero replicas, and increase it simultaneously with decreasing old deployment replicas. In the result it end up with zero replicas at old deployment.
### Blue Green
You create an additional deployment with new version of application, and after testing you redirect service from old to new.
### Canary
You create an additional deployment and distribute small amount of traffic to it, and after ensuring that everything is good you move all traffic to a new deployment.
# Jobs
```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: throw-dice-job
spec:
  completions: 3 # 3 successfull run before finish
  backoffLimit: 40 # 40 attempts
  parallelism: 3 # run concurrently
  template:
    metadata:
      creationTimestamp: null
    spec:
      containers:
      - image: kodekloud/throw-dice
        name: throw-dice-job
        resources: {}
      restartPolicy: Never
```
To get attempts number run `kubectl desctibe job myJob` and find the line `Pods Statuses:    0 Active (0 Ready) / 1 Succeeded / 3 Failed`
# CronJob
```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  creationTimestamp: null
  name: throw-dice-cron-job
spec:
  jobTemplate:
    metadata:
      creationTimestamp: null
      name: throw-dice-cron-job
    spec:
	  backoffLimit: 25
	  completions: 1
	  activeDeadlineSeconds: 20
      template:
        metadata:
          creationTimestamp: null
        spec:
          containers:
          - image: kodekloud/throw-dice
            name: throw-dice-cron-job
            resources: {}
          restartPolicy: OnFailure
  schedule: 30 21 * * * # run at 21:30 every day
status: {}
```
# Networking
Expose deployment 
```
kubectl expose deployment redis --name=redis --port=6379 --target-port=6379 
```
## Services
### NodePort
NodePort service listens to a specific port within a Node and forward requests to a Pod.
nodePort range is 30000-32767.
In case of multiple Pods Service will distribute traffic across all Pods that match the Selector. 
If those Pods are deployed on different Nodes, Service will distribute traffic the same way like it deals with one Node. For instance user sends a request to Node1 and it can land at Pod2.
Disadvantages:
- Doesn't provide automatic load balancing across nodes.
- Node IP Dependency: Clients need to know the IP addresses of the nodes to access the service. This can be challenging in dynamic environments where nodes may be added or removed
- **Security Implications:** Exposing services on high ports (in the 30000-32767 range) might expose them to security risks, as these ports are accessible externally.

```
apiVersion: v1
kind: Service
metadata:
  name: webapp-service
spec:
  ports:
  - nodePort: 30080 # publicly accessible port
    port: 8080 # service port
    targetPort: 8080 # Pod's port
  selector:
    name: simple-webapp
  type: NodePort
```
Another way:
```
apiVersion: v1
kind: Service
metadata:
  name: webhook-server
  namespace: webhook-demo
spec:
  selector:
    app: webhook-server
  ports:
    - port: 443
      targetPort: webhook-api
```
When Pod definition is:
```
...
spec:
  containers:
  - name: server
	image: myImageName
	ports:
	- containerPort: 8443
	  name: webhook-api
```
### ClusterIP
Creates a virtual IP inside a cluster to enable communication between services. Despite the ability to connect to pod(or set of pods) via label, ClusterIP is a recommended way to do so.
### LoadBalancer
Bare metal k8s cluster requires additional solution like MetalLB for public access. Sometimes NodePort service is used in this case.
Cloud Platforms do the provision of their external load balancer to the LoadBalancer service.
## Ingress
- When `LoadBalancer` operates on transport(4) layer and works with any type of traffic, `Ingress` operates on Application(7) layer and is specifically designed for HTTP and HTTPS traffic.
- Ingress provides more sophisticated routing capabilities
- You need to separately deploy and configure an Ingress controller(which could be based on Nginx, Traefik, etc.)

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  creationTimestamp: null
  name: my-ingress
spec:
  rules:
  - host: art.com
    http:
      paths:
      - backend:
          service:
            name: art-service
            port:
              number: 8080
        path: /path
        pathType: Exact
status:
  loadBalancer: {}
```
If we need to redirect `host/path` to just `service-name/`  without additional path - we can use ReWrite target:
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: minimal-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
```
One more example:
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-vh-routing
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: "watch.ecom-store.com"
    http:
      paths:
      - path: /video
        pathType: Prefix
        backend:
          service:
            name: video-service
            port:
              number: 8080
  - host: "apparels.ecom-store.com"
    http:
      paths:
      - path: /wear
        pathType: Prefix
        backend:
          service:
            name: apparels-service
            port:
              number: 8080
```
## NetworkPolicy
All pods within the same Virtual Private Network can access each other. But if you need to allow requests to `db-service` only from `backend-service`  you can use NetworkPolicy.
NetworkPolicy can restrict both Ingress(Inbound) and Egress(Outbound) requests.
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: internal-policy
  namespace: default
spec:
  podSelector:
    matchLabels:
      name: internal
  policyTypes:
  - Egress
  - Ingress
  ingress:
  - from:
      - podSelector:
          matchLabels:
            name: webapp-color
	ports:
		- protocol: TCP
		  port: 80
  egress:
  - to:
    - podSelector:
        matchLabels:
          name: mysql
    ports:
    - protocol: TCP
      port: 3306
  - to:
    - podSelector:
        matchLabels:
          name: payroll
    ports:
    - protocol: TCP
      port: 8080
  - ports:
    - port: 53
      protocol: UDP
    - port: 53
      protocol: TCP
```
# Volumes
## Volumes and Claims
Persistent Volume (PV) and Persistent Volume Claim (PVC) are two different objects, PV is created by k8s cluster admin, and PVC is created by cluster user.
- Each PV can be bind to single PVC(1 to 1 relation)
- In case of binding bigger PV to PVC, no other PVC can use non-used PV Capacity.
- PVC goes to Pending state if there is no free PV for it.
### PersistentVolumeReclaimPolicy
#### Retain
The `Retain` reclaim policy allows for manual reclamation of the resource. When the PersistentVolumeClaim is deleted, the PersistentVolume still exists and the volume is considered "released". But it is not yet available for another claim because the previous claimant's data remains on the volume. An administrator can manually reclaim the volume with the following steps.
1. Delete the PersistentVolume. The associated storage asset in external infrastructure still exists after the PV is deleted.
2. Manually clean up the data on the associated storage asset accordingly.
3. Manually delete the associated storage asset.
If you want to reuse the same storage asset, create a new PersistentVolume with the same storage asset definition.
#### Delete
For volume plugins that support the `Delete` reclaim policy, deletion removes both the PersistentVolume object from Kubernetes, as well as the associated storage asset in the external infrastructure. Volumes that were dynamically provisioned inherit the [reclaim policy of their StorageClass](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#reclaim-policy), which defaults to `Delete`. The administrator should configure the StorageClass according to users' expectations; otherwise, the PV must be edited or patched after it is created. See [Change the Reclaim Policy of a PersistentVolume](https://kubernetes.io/docs/tasks/administer-cluster/change-pv-reclaim-policy/).
#### Recycle
**Warning:** The `Recycle` reclaim policy is deprecated. Instead, the recommended approach is to use dynamic provisioning.
If supported by the underlying volume plugin, the `Recycle` reclaim policy performs a basic scrub (`rm -rf /thevolume/*`) on the volume and makes it available again for a new claim.

### Create PV and PVC and mount it to Pod
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: webapp
spec:
  containers:
  - name: event-simulator
    image: kodekloud/event-simulator
    volumeMounts:
    - name: log-volume # to which volume
      mountPath: /log # which pod dir

  volumes:
  - name: log-volume
    hostPath:
      path: /var/log/webapp # host path, not pod
```

To use PV do the following:
1. Create PV:
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-log
spec:
  capacity:
    storage: 100Mi
  accessModes:
    - ReadWriteMany
  storageClassName: manual
  persistentVolumeReclaimPolicy: Retain
  hostPath:
   path: /pv/log
```
2. Create PVC:
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: claim-log-1
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: manual
  resources:
    requests:
      storage: 50Mi
```
3. Update pod to use PVC as its storage:
```yaml
apiVersion: v1
kind: Pod
...
spec:
  containers:
  - volumeMounts:
    - name: local-pv # to which volume
      mountPath: /log # which pod dir
  volumes:
  - name: local-pv
    persistentVolumeClaim:
      claimName: local-pvc
```

If you try to delete PVC used by POD, it stacks in `Terminating` state.
## Storage Classes
Enables dynamic storage provisioning for Public Clouds. Without it you should create manually disk at Public Cloud for each PV.
It automatically create PV, so for letting Pod use storage we need need only Pod, PVC and SC(Storage Class) definition
```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: delayed-volume-sc
provisioner: kubernetes.io/no-provisioner
volumeBindingMode: WaitForFirstConsumer
```
## StatefulSet
Case: we need to deploy MySQL with 1 leader and 2 followers. And we want to deploy Leader first, then first Follower, which will replicate all data from master, and only after that we will deploy second Follower, which will replicate all data from the (!) First Follower(To reduce Leader load). And only after full replication the Second Follower will start continuous replication from the Leader. 
Here we can employ StatefulSet - it will provide us ordered scale up and scale down. Each new replica will sync data from the previous one and so on. And in case of scale down Leader will remain the last one.
But you can also define Parallel approach of deployment. 
```
apiVersion: v1
kind: Service
metadata:
  name: redis-cluster-service
spec:
  ports:
  - port: 6379
    targetPort: 6379
    name: client
  - port: 16379
    targetPort: 16379
    name: gossip
  selector:
    app: redis-cluster
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: redis-cluster
spec:
  selector:
    matchLabels:
      app: redis-cluster # has to match .spec.template.metadata.labels
  serviceName: "redis-cluster-service"
  replicas: 6 # by default is 1
  #  minReadySeconds: 10 # by default is 0
  template:
    metadata:
      labels:
        app: redis-cluster # has to match .spec.selector.matchLabels
    spec:
      terminationGracePeriodSeconds: 10
      containers:
      - name: redis
        image: redis:5.0.1-alpine
        command: ["/conf/update-node.sh", "redis-server", "/conf/redis.conf"]
        env:
        - name: POD_IP
          valueFrom:
            fieldRef:
              fieldPath: status.podIP
        ports:
        - containerPort: 6379
          name: client
        - containerPort: 16379 
          name: gossip
        volumeMounts:
        - name: conf
          mountPath: /conf
          readOnly: false
        - name: data
          mountPath: /data
          readOnly: false
      volumes:
      - name: conf
        configMap:
          name: redis-cluster-configmap
          defaultMode: 0755
  volumeClaimTemplates:
  - metadata:
      name: data
    spec:
      accessModes: [ "ReadWriteOnce" ]
      storageClassName: ""
      resources:
        requests:
          storage: 1Gi
```
## Headless service
In Kubernetes, a headless service is a type of service that does not allocate a cluster IP address to itself. Unlike regular services, which expose a single IP address for a set of pods, **a headless service allows each pod to have its own DNS record.**

**Use Cases Beyond StatefulSets:**
While commonly associated with StatefulSets, headless services can be useful in scenarios where direct pod-to-pod communication or dynamic DNS-based discovery is preferred.
# Security
All(kubectl, curl) user requests to cluster goes through `kube-apiserver`. It is responsible for authentication.
## Kubeconfig
Holds the auth information for cli. 
It has:
- data about users(keys and certificates)
- cluster(server url)
- `context` - user@cluster binding(including namespace, if needed).
```bash
# set context from non-default config file:
kubectl config use-context --kubeconfig=./my-kube-config research
# Where research is context name
```
`--kubeconfig` doesn't have autocompletion
## kube-apiserver
To get its config metadata:
`cat /etc/kubernetes/manifests/kube-apiserver.yaml`

If you made a change and k8s doesn't work:
1. `kube-apiserver` is what you need
- you can find it logs at `/var/log/pods` or `/var/log/containers`
-  `watch crictl ps` will show you running containers. 
## API group
There are two API groups - core(`/api`) and named(`/apis`).
API groups -> Resources -> Actions(Get, List, Delete, etc.)
Hierarchy:
- core(`/api`):
	- `/v1`
		- `Pods, Namespaces, RS, Events, Endpoints, Nodes, Bindings, PV, PVC, Configmaps, Secrets, Services`
- named(`/apis`)
	- `/apps/v1`
		- `/deployments, RS, statefulsets`
	- `/extensions`
	- `/networking.k8s.io/v1`
		- `networkpolicies`
	- `/storage.k8s.io`
	- `/authentication.k8s.io`
	- `/certificates.k8s.io`
## Authorization modes
- Node(is used by kubelet)
- ABAC(Attribute Based Access Control) - for each user or group you define set of permissions. Requires kubeapi restart.
- RBAC(Role Based Access Control) - you bind role for each user or group. Changes are applied immediately on a fly. Recommended.
- Webhook(delegate auth check to third party)
- Always Allow(default one at kubeapi server)
- Always Deny
When you have multiple Modes configured, your request will be authorised using each one in the order it is specified.
**To get modes, just describe pod and you will see flags used for kube-apiserver**
## RBAC
### Role
- Defines the set of permissions for each resource.
- For core(`/api`) `apiGroups` is "", 
```bash
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: developer
rules:
- apiGroups:
  - ""
  resources:
  - pods
  verbs:
  - list
  - create
  - delete
```
### RoleBindings
Links a User object to a Role.
```bash
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: dev-user-binding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: developer
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: dev-user
```

Each user can check if he is eligible for an action:
```yaml
kubeclt auth can-i create deployment -n prod
```
Admin can impersonate a specific user to check permissions:
```yaml
kubeclt auth can-i create deployment --as dev-user
```
## Cluster Roles
While Roles and Rolebindings lives under the scope of namespace, Clusterroles and Clusterrolebindings are cluster-wide(global) and can give you an access to a resource across all namespaces.
## Admission Controlles
Help us to achieve better security measures, validate configuration and perform additional operations before Pod is created. Examples:
- Allow only specific registry for docker images
- Make labels required
- Forbid using :latest tag
- etc
### Prebuilt Admission Controllers
Can be enabled by default. You can set it in flags of `kube-apiserver`.
- AlwaysPullImages
- DefaultStorageClass
- EventRateLimit
- NamespaceExists(Enabled by default)
- NamespaceAutoProvision(Automatically creates a NS if not exists)
- many more

All Admission Controllers can be divided into two groups:
- Mutating
- Validating
Note, that Mutating AC are executed before Validating
### Own Admission Controllers
- MutatingAdmissionWebhook
- ValidatingAdmissionWebhook
We can configure our kube-apiserver to check the request against our Webhook.
So input is an Admission Review Object, which has all the details about the request.
Output is an Admission Review Object with the result if request is allowed or not.
To achieve this we need:
1. Deploy a Webhook Server.
	- It should have `/mutate` and `/validate` endpoints
2. Create a Webhook Configuration Object:
```
apiVersion: admissionregistration.k8s.io/v1
kind: MutatingWebhookConfiguration
metadata:
  name: demo-webhook
webhooks:
  - name: webhook-server.webhook-demo.svc
    clientConfig:
      service:
        name: webhook-server
        namespace: webhook-demo
        path: "/mutate"
      caBundle: LS0tLS1...JopS
    rules:
      - operations: [ "CREATE" ]
        apiGroups: [""]
        apiVersions: ["v1"]
        resources: ["pods"]
    admissionReviewVersions: ["v1beta1"]
    sideEffects: None
```
## API Versions
Single kind can have multiple versions available line v1alpha1, v1beta1, v1.
You can define it in manifest.
But even though there are multiple versions supported in the same time, only one can be supported in `Preffered` and `Storage` version.
- `Preferred` version defines which version is the kubectl command going to query.
- `Storage` version defines the data representation in ETCD.
### kubectl convert
Is a plugin that may be absent in your install, it gives you a possibility to upgrade a version in a resource manifest.
```
kubectl-convert -f ingress-old.yaml --output-version networking.k8s.io/v1 > ingress-new.yaml
```
## Custom Resource Definition
- Can be  namespace or cluster scoped
Gives us an ability to specify custom Resource and make CRUD operations with it.
To create such a resource we should:
1. Create a CRD manifest:
```yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  name:  internals.datasets.kodekloud.com
spec:
  group: datasets.kodekloud.com
  versions:
    - name: v1
      served: true
      storage: true
      schema:
        openAPIV3Schema:
          type: object
          properties:
            spec:
              type: object
              properties:
                internalLoad:
                  type: string
                range:
                  type: integer
                percentage:
                  type: string
  scope: Namespaced
  names:
    plural: internals
    singular: internal
    kind: Internal
    shortNames:
    - int
```
2. Create a custom resource manifest:
```yaml
kind: Internal
apiVersion: datasets.kodekloud.com/v1
metadata:
  name: internal-space
  namespace: default
spec:
  internalLoad: "high"
  range: 80
  percentage: "50"
```
### Custom Controller
Watches the changes in CR and takes actions accordingly(defines the logic of CR). 
Example: Distributed Database which is not supported natively in K8S(For instance, Apache Cassandra).
We 
You can write code in Golang, wrap it in docker and run as a pod.
### Operator Framework
Real world case - ETCD Operator.
Groups both CRD and Custom Controller and deals with it as a single entity. Additionally it can be responsible for backups, restoring, etc.
All operators are available in the Operator Hub.
# Helm
K8S package, release manager.
Each custom app(Wordpress) needs exact WP app pod, DB Pod, Service Account, etc.
Instead of dealing with multiple yaml files, you can use Helm Charts, and focus only on the variables that you want to set(UserName, UserPass, Storage, etc).
So, Helm chart consists of Templates(k8s manifests) and values.yaml file that holds exact variables.
```yaml
helm search hub wordpress # search the default repo Artifacthub.io

helm repo add bitnami https://charts.bitnami.com/bitnami # Add custon repo

helm search repo wordpress # search in manually added repos

helm list # Lists all of the local releases 

helm install NAME REPO_NAME... # installs chart with the default values from remote or local dir


helm pull --untar # pulls chart for further editing.

helm uninstall bravo # drops local release
```
Mock exams:
```yaml
# 1. delete the required release:
helm -n mercury uninstall internal-issue-report-apiv1

# 2. upgrade a release
helm repo list
helm repo update
helm search repo nginx
helm -n mercury upgrade internal-issue-report-apiv2 bitnami/nginx

# 3. Install release and set replica 3
# Check in color the exact valune name:
helm show values bitnami/apache | yq e | grep replica
# in case of nested level use dot `--set image.debug=true`
# Install a release
helm -n mercury install internal-issue-report-apache bitnami/apache --set replicaCount=2

```
# Mock exams mistakes
How to run binary
```
...
  - command:
	- sh 
	- -c
	- "cowsay I am going to ace CKAD!"
```

Dont forget about nodeport setting:
```
  - nodePort: 30083
    port: 80
    protocol: TCP
    targetPort: 30083
```
 
Persistent Volume hostPath:
```
kind: PersistentVolume
apiVersion: v1
metadata:
  name: hostpath2
  labels:
    type: local
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  reclaimPolicy:
    - Recycle
  hostPath:
    path: "/tmp/data1"
```

## Linux foundation mocks
write logs from file into stdout
```
command: ["sh", "-c", "tail -f /var/log/cleaner/cleaner.log"]
```
## Service
- Service should point to Pod, not a Deployment!
- svc should have endpoints
```
k get endpoints
```
- if you need to reach svc outside its namespace - just add ns name in the end of svc name, separated by dot.(svc is in mars ns):
```
k run tmp --restart=Never --rm -i --image=nginx:alpine -- curl manager-api-svc.mars:4444
```

scenario 20!