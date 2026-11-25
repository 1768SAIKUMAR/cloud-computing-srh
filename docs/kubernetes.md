# Session 4: the generator on GKE

## kubectl get pods -o wide (3 replicas)
```
NAME                        READY   STATUS    RESTARTS   AGE   IP           NODE                             NOMINATED NODE   READINESS GATES
generator-c4659666b-5fwrl   1/1     Running   0          15s   10.92.0.17   gke-freight-main-3e5c9885-8jvd   <none>           <none>
generator-c4659666b-llncj   1/1     Running   0          15s   10.92.0.16   gke-freight-main-3e5c9885-8jvd   <none>           <none>
generator-c4659666b-tzc7v   1/1     Running   0          48s   10.92.0.15   gke-freight-main-3e5c9885-8jvd   <none>           <none>
```

## kubectl rollout history deployment/generator
```
deployment.apps/generator 
REVISION  CHANGE-CAUSE
1         <none>
2         <none>

```

## What happened when I deleted the pod

When I deleted the pod `generator-c4659666b-x8xvd`, Kubernetes created a new pod `generator-c4659666b-tzc7v` within about one second, without me doing anything.
This happened because the Deployment declares the desired state of `replicas: 1`. After the delete the actual state was 0 pods, so the Deployment's controller noticed the difference and started a new pod to bring the actual state back to the desired state.
This control loop (desired state vs actual state) is why Kubernetes can heal itself, for example when a pod crashes or a node fails.
