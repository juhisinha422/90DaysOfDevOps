# Day 51 – Kubernetes Manifests and Your First Pods

## Task 1: Create Your First Pod (Nginx)

### Create a file called nginx-pod.yaml:

```bash
ubuntu@ip-172-31-6-80:~/k8s$ batcat nginx-pod.yaml
───────┬──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
       │ File: nginx-pod.yaml
───────┼──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
   1   │ apiVersion: v1
   2   │ kind: Pod
   3   │ metadata:
   4   │   name: nginx-pod
   5   │   labels:
   6   │     app: nginx
   7   │ spec:
   8   │   containers:
   9   │   - name: nginx
  10   │     image: nginx:latest
  11   │     ports:
  12   │     - containerPort: 80
───────┴──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
ubuntu@ip-172-31-6-80:~/k8s$ kubectl apply -f nginx-pod.yaml
pod/nginx-pod created
ubuntu@ip-172-31-6-80:~/k8s$ kubectl get pods
NAME        READY   STATUS    RESTARTS   AGE
nginx-pod   1/1     Running   0          2m29s
ubuntu@ip-172-31-6-80:~/k8s$ kubectl get pods -o wide
NAME        READY   STATUS    RESTARTS   AGE     IP           NODE                           NOMINATED NODE   READINESS GATES
nginx-pod   1/1     Running   0          2m38s   10.244.0.5   devops-cluster-control-plane   <none>           <none>
ubuntu@ip-172-31-6-80:~/k8s$
```

```bash
# Detailed info about the pod
ubuntu@ip-172-31-6-80:~/k8s$ kubectl describe pod nginx-pod
Name:             nginx-pod
Namespace:        default
Priority:         0
Service Account:  default
Node:             devops-cluster-control-plane/172.20.0.2
Start Time:       Tue, 08 Sep 2026 09:03:11 +0000
Labels:           app=nginx
Annotations:      <none>
Status:           Running
IP:               10.244.0.5
IPs:
  IP:  10.244.0.5
Containers:
  nginx:
    Container ID:   containerd://133edfd3ddaf51dcfd5b899657da5c224d8d3b729dc3b469f45d7cf283cb7bef
    Image:          nginx:latest
    Image ID:       docker.io/library/nginx@sha256:05b8cb60c354a44ab824ea6e7dc69b46d50762cdbe728a347a5b656e6fb3d7c4
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Tue, 08 Sep 2026 09:03:15 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-lt4fj (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True
  Initialized                 True
  Ready                       True
  ContainersReady             True
  PodScheduled                True
Volumes:
  kube-api-access-lt4fj:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    Optional:                false
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age    From               Message
  ----    ------     ----   ----               -------
  Normal  Scheduled  5m38s  default-scheduler  Successfully assigned default/nginx-pod to devops-cluster-control-plane
  Normal  Pulling    5m37s  kubelet            spec.containers{nginx}: Pulling image "nginx:latest"
  Normal  Pulled     5m34s  kubelet            spec.containers{nginx}: Successfully pulled image "nginx:latest" in 3.034s (3.034s including waiting). Image size: 66343926 bytes.
  Normal  Created    5m34s  kubelet            spec.containers{nginx}: Container created
  Normal  Started    5m34s  kubelet            spec.containers{nginx}: Container started
ubuntu@ip-172-31-6-80:~/k8s$

```

```bash
# Read the logs
ubuntu@ip-172-31-6-80:~/k8s$ kubectl logs nginx-pod
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2026/09/08 09:03:15 [notice] 1#1: using the "epoll" event method
2026/09/08 09:03:15 [notice] 1#1: nginx/1.31.5
2026/09/08 09:03:15 [notice] 1#1: built by gcc 14.2.0 (Debian 14.2.0-19)
2026/09/08 09:03:15 [notice] 1#1: OS: Linux 7.0.0-1012-aws
2026/09/08 09:03:15 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 2147483584:2147483584
2026/09/08 09:03:15 [notice] 1#1: start worker processes
2026/09/08 09:03:15 [notice] 1#1: start worker process 36
2026/09/08 09:03:15 [notice] 1#1: start worker process 37
ubuntu@ip-172-31-6-80:~/k8s$

```

```bash
# Get a shell inside the container

ubuntu@ip-172-31-6-80:~/k8s$ kubectl exec -it nginx-pod -- /bin/bash
root@nginx-pod:/# ls
bin   dev                  docker-entrypoint.sh  home  lib64  mnt  proc          product_uuid  run   srv  tmp  var
boot  docker-entrypoint.d  etc                   lib   media  opt  product_name  root          sbin  sys  usr
root@nginx-pod:/# cd var
root@nginx-pod:/var# ls
backups  cache  lib  local  lock  log  mail  opt  run  spool  tmp
root@nginx-pod:/var#
root@nginx-pod:/var#

```

## Task 2: Create a Custom Pod (BusyBox)

Write a new manifest busybox-pod.yaml from scratch

```bash

ubuntu@ip-172-31-6-80:~/k8s$ ls
busybox-pod.yaml  nginx-pod.yaml
ubuntu@ip-172-31-6-80:~/k8s$ batcat busybox-pod.yaml
───────┬──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
       │ File: busybox-pod.yaml
───────┼──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
   1   │ apiVersion: v1
   2   │ kind: Pod
   3   │ metadata:
   4   │   name: busybox-pod
   5   │   labels:
   6   │     app: busybox
   7   │     environment: dev
   8   │ spec:
   9   │   containers:
  10   │   - name: busybox
  11   │     image: busybox:latest
  12   │     command: ["sh", "-c", "echo Hello from BusyBox && sleep 3600"]
───────┴──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
ubuntu@ip-172-31-6-80:~/k8s$ kubectl apply -f busybox-pod.yaml
pod/busybox-pod created
ubuntu@ip-172-31-6-80:~/k8s$ kubectl get pods
NAME          READY   STATUS    RESTARTS   AGE
busybox-pod   1/1     Running   0          8s
nginx-pod     1/1     Running   0          15m
ubuntu@ip-172-31-6-80:~/k8s$
ubuntu@ip-172-31-6-80:~/k8s$ kubectl logs busybox-pod
Hello from BusyBox
ubuntu@ip-172-31-6-80:~/k8s$

```
Verified: BusyBox logs. ✅

## Task 3: Imperative vs Declarative
### Create a pod without a YAML file

```bash

ubuntu@ip-172-31-6-80:~$ kubectl run redis-pod --image=redis:latest
pod/redis-pod created
ubuntu@ip-172-31-6-80:~$ kubectl get pods
NAME          READY   STATUS    RESTARTS        AGE
busybox-pod   1/1     Running   3 (9m19s ago)   3h9m
nginx-pod     1/1     Running   0               3h24m
redis-pod     1/1     Running   0               9s
ubuntu@ip-172-31-6-80:~$

```
Extracting the YAML generated by Kubernetes

```bash

ubuntu@ip-172-31-6-80:~$ kubectl get pod redis-pod -o yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2026-09-08T12:27:52Z"
  generation: 1
  labels:
    run: redis-pod
  name: redis-pod
  namespace: default
  resourceVersion: "29568"
  uid: 3c5cac3b-44c4-4684-88a8-25181d6b3614
spec:
  containers:
  - image: redis:latest
    imagePullPolicy: Always
    name: redis-pod
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-q4dvm
      readOnly: true
  dnsPolicy: ClusterFirst
  enableServiceLinks: true
  nodeName: devops-cluster-control-plane
  preemptionPolicy: PreemptLowerPriority
  priority: 0
  restartPolicy: Always
  schedulerName: default-scheduler
  securityContext: {}
  serviceAccount: default
  serviceAccountName: default
  terminationGracePeriodSeconds: 30
  tolerations:
  - effect: NoExecute
    key: node.kubernetes.io/not-ready
    operator: Exists
    tolerationSeconds: 300
  - effect: NoExecute
    key: node.kubernetes.io/unreachable
    operator: Exists
    tolerationSeconds: 300
  volumes:
  - name: kube-api-access-q4dvm
    projected:
      defaultMode: 420
      sources:
      - serviceAccountToken:
          expirationSeconds: 3607
          path: token
      - configMap:
          items:
          - key: ca.crt
            path: ca.crt
          name: kube-root-ca.crt
      - downwardAPI:
          items:
          - fieldRef:
              apiVersion: v1
              fieldPath: metadata.namespace
            path: namespace
status:
  conditions:
  - lastProbeTime: null
    lastTransitionTime: "2026-09-08T12:27:53Z"
    observedGeneration: 1
    status: "True"
    type: PodReadyToStartContainers
  - lastProbeTime: null
    lastTransitionTime: "2026-09-08T12:27:52Z"
    observedGeneration: 1
    status: "True"
    type: Initialized
  - lastProbeTime: null
    lastTransitionTime: "2026-09-08T12:27:55Z"
    observedGeneration: 1
    status: "True"
    type: Ready
  - lastProbeTime: null
    lastTransitionTime: "2026-09-08T12:27:55Z"
    observedGeneration: 1
    status: "True"
    type: ContainersReady
  - lastProbeTime: null
    lastTransitionTime: "2026-09-08T12:27:52Z"
    observedGeneration: 1
    status: "True"
    type: PodScheduled
  containerStatuses:
  - containerID: containerd://7d07877f6f8a64d476154c28bc6d285019ffdf62654e6ae39d7d87e896fc7c99
    image: docker.io/library/redis:latest
    imageID: docker.io/library/redis@sha256:298e5b3bc566bade82f46ad5511777a4a07a294097ce16ada2f6a42be5239df5
    lastState: {}
    name: redis-pod
    ready: true
    resources: {}
    restartCount: 0
    started: true
    state:
      running:
        startedAt: "2026-09-08T12:27:55Z"
    user:
      linux:
        gid: 0
        supplementalGroups:
        - 0
        uid: 0
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-q4dvm
      readOnly: true
      recursiveReadOnly: Disabled
  hostIP: 172.20.0.2
  hostIPs:
  - ip: 172.20.0.2
  observedGeneration: 1
  phase: Running
  podIP: 10.244.0.7
  podIPs:
  - ip: 10.244.0.7
  qosClass: BestEffort
  resources: {}
  startTime: "2026-09-08T12:27:52Z"
ubuntu@ip-172-31-6-80:~$
```

```bash
ubuntu@ip-172-31-6-80:~$ kubectl run test-pod --image=nginx --dry-run=client -o yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: test-pod
  name: test-pod
spec:
  containers:
  - image: nginx
    name: test-pod
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
ubuntu@ip-172-31-6-80:~$

```
### Verify: Save the dry-run output to a file and compare its structure with your nginx-pod.yaml. What fields are the same? What is different?
The generated YAML and nginx-pod.yaml have the same basic Kubernetes structure, including apiVersion, kind, metadata, spec, containers, and the Nginx image. The main differences are the Pod/container names and any additional configuration fields that were manually added to nginx-pod.yaml.

## Task 4: Validate Before Applying

Before applying a manifest, you can validate it:

```bash
ubuntu@ip-172-31-6-80:~$ ls
Ecommerce-Website  k8s
ubuntu@ip-172-31-6-80:~$ cd k8s
ubuntu@ip-172-31-6-80:~/k8s$ ls
busybox-pod.yaml  nginx-pod.yaml
ubuntu@ip-172-31-6-80:~/k8s$ kubectl apply -f nginx-pod.yaml --dry-run=client
pod/nginx-pod unchanged (dry run)
ubuntu@ip-172-31-6-80:~/k8s$ kubectl apply -f nginx-pod.yaml --dry-run=server
pod/nginx-pod unchanged (server dry run)
ubuntu@ip-172-31-6-80:~/k8s$
ubuntu@ip-172-31-6-80:~/k8s$
ubuntu@ip-172-31-6-80:~/k8s$ ls
busybox-pod.yaml  nginx-pod.yaml
ubuntu@ip-172-31-6-80:~/k8s$
ubuntu@ip-172-31-6-80:~/k8s$
ubuntu@ip-172-31-6-80:~/k8s$
ubuntu@ip-172-31-6-80:~/k8s$ vim nginx-pod.yaml
ubuntu@ip-172-31-6-80:~/k8s$
ubuntu@ip-172-31-6-80:~/k8s$
ubuntu@ip-172-31-6-80:~/k8s$ kubectl apply -f nginx-pod.yaml --dry-run=server
The Pod "nginx-pod" is invalid: spec.containers[0].image: Required value
ubuntu@ip-172-31-6-80:~/k8s$
```
### Verify: What error does Kubernetes give when the image field is missing?
When the image field is missing, Kubernetes creates the Pod object but cannot start the container because no container image is specified. The Pod enters CreateContainerConfigError.

## Task 5: Pod Labels and Filtering

```bash
# List all pods with their labels
ubuntu@ip-172-31-6-80:~/k8s$ kubectl get pods --show-labels
NAME          READY   STATUS    RESTARTS      AGE     LABELS
busybox-pod   1/1     Running   3 (33m ago)   3h33m   app=busybox,environment=dev
nginx-pod     1/1     Running   0             3h48m   app=nginx
redis-pod     1/1     Running   0             23m     run=redis-pod
# Filter pods by label
ubuntu@ip-172-31-6-80:~/k8s$ kubectl get pods -l app=nginx
NAME        READY   STATUS    RESTARTS   AGE
nginx-pod   1/1     Running   0          3h48m
ubuntu@ip-172-31-6-80:~/k8s$ kubectl get pods -l environment=dev
NAME          READY   STATUS    RESTARTS      AGE
busybox-pod   1/1     Running   3 (33m ago)   3h33m
# Add a label to an existing pod
ubuntu@ip-172-31-6-80:~/k8s$ kubectl label pod nginx-pod environment=production
pod/nginx-pod labeled
# Verify
ubuntu@ip-172-31-6-80:~/k8s$ kubectl get pods --show-labels
NAME          READY   STATUS    RESTARTS      AGE     LABELS
busybox-pod   1/1     Running   3 (34m ago)   3h34m   app=busybox,environment=dev
nginx-pod     1/1     Running   0             3h49m   app=nginx,environment=production
redis-pod     1/1     Running   0             24m     run=redis-pod
# Remove a label
ubuntu@ip-172-31-6-80:~/k8s$ kubectl label pod nginx-pod environment-
pod/nginx-pod unlabeled
ubuntu@ip-172-31-6-80:~/k8s$
```

### Write a manifest for a third pod with at least 3 labels (app, environment, team). Apply it and practice filtering.

```bash

ubuntu@ip-172-31-6-80:~/k8s$ vim third-pod.yaml
ubuntu@ip-172-31-6-80:~/k8s$ batcat third-pod.yaml
───────┬──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
       │ File: third-pod.yaml
───────┼──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
   1   │ apiVersion: v1
   2   │ kind: Pod
   3   │ metadata:
   4   │   name: third-pod
   5   │   labels:
   6   │     app: nginx
   7   │     environment: development
   8   │     team: devops
   9   │ spec:
  10   │   containers:
  11   │     - name: nginx
  12   │       image: nginx
───────┴──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
ubuntu@ip-172-31-6-80:~/k8s$ kubectl apply -f third-pod.yaml
pod/third-pod created
ubuntu@ip-172-31-6-80:~/k8s$ kubectl get pods --show-labels
NAME          READY   STATUS    RESTARTS      AGE     LABELS
busybox-pod   1/1     Running   3 (42m ago)   3h42m   app=busybox,environment=dev
nginx-pod     1/1     Running   0             3h57m   app=nginx
redis-pod     1/1     Running   0             32m     run=redis-pod
third-pod     1/1     Running   0             10s     app=nginx,environment=development,team=devops
ubuntu@ip-172-31-6-80:~/k8s$ kubectl get pods -l app=nginx
NAME        READY   STATUS    RESTARTS   AGE
nginx-pod   1/1     Running   0          3h57m
third-pod   1/1     Running   0          28s
ubuntu@ip-172-31-6-80:~/k8s$ kubectl get pods -l environment=development
NAME        READY   STATUS    RESTARTS   AGE
third-pod   1/1     Running   0          78s
ubuntu@ip-172-31-6-80:~/k8s$ kubectl get pods -l team=devops
NAME        READY   STATUS    RESTARTS   AGE
third-pod   1/1     Running   0          110s
ubuntu@ip-172-31-6-80:~/k8s$
ubuntu@ip-172-31-6-80:~/k8s$ kubectl describe pod third-pod
Name:             third-pod
Namespace:        default
Priority:         0
Service Account:  default
Node:             devops-cluster-control-plane/172.20.0.2
Start Time:       Tue, 08 Sep 2026 13:00:38 +0000
Labels:           app=nginx
                  environment=development
                  team=devops
Annotations:      <none>
Status:           Running
IP:               10.244.0.8
IPs:
  IP:  10.244.0.8
Containers:
  nginx:
    Container ID:   containerd://939ce5f7a94722fda755e49e77b29c29dfb450497a6f754dce28fcde27c17f69
    Image:          nginx
    Image ID:       docker.io/library/nginx@sha256:05b8cb60c354a44ab824ea6e7dc69b46d50762cdbe728a347a5b656e6fb3d7c4
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Tue, 08 Sep 2026 13:00:39 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-65hx5 (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True
  Initialized                 True
  Ready                       True
  ContainersReady             True
  PodScheduled                True
Volumes:
  kube-api-access-65hx5:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    Optional:                false
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age    From               Message
  ----    ------     ----   ----               -------
  Normal  Scheduled  3m     default-scheduler  Successfully assigned default/third-pod to devops-cluster-control-plane
  Normal  Pulling    2m59s  kubelet            spec.containers{nginx}: Pulling image "nginx"
  Normal  Pulled     2m59s  kubelet            spec.containers{nginx}: Successfully pulled image "nginx" in 554ms (554ms including waiting). Image size: 66343926 bytes.
  Normal  Created    2m59s  kubelet            spec.containers{nginx}: Container created
  Normal  Started    2m59s  kubelet            spec.containers{nginx}: Container started

```
## Task 6: Clean Up

```bash
# Delete by name
ubuntu@ip-172-31-6-80:~/k8s$ kubectl delete pod nginx-pod
pod "nginx-pod" deleted from default namespace
ubuntu@ip-172-31-6-80:~/k8s$ kubectl delete pod busybox-pod
pod "busybox-pod" deleted from default namespace
ubuntu@ip-172-31-6-80:~/k8s$ kubectl delete pod redis-pod
pod "redis-pod" deleted from default namespace
ubuntu@ip-172-31-6-80:~/k8s$ kubectl get pods
NAME        READY   STATUS    RESTARTS   AGE
third-pod   1/1     Running   0          9m22s
ubuntu@ip-172-31-6-80:~/k8s$ ls
busybox-pod.yaml  nginx-pod.yaml  third-pod.yaml

# Or delete using the manifest file
ubuntu@ip-172-31-6-80:~/k8s$ kubectl delete -f third-pod.yaml
pod "third-pod" deleted from default namespace
ubuntu@ip-172-31-6-80:~/k8s$
# Verify everything is gone
ubuntu@ip-172-31-6-80:~/k8s$ kubectl get pods
No resources found in default namespace.
ubuntu@ip-172-31-6-80:~/k8s$
ubuntu@ip-172-31-6-80:~/k8s$
ubuntu@ip-172-31-6-80:~/k8s$

```
