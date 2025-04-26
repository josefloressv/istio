# Kubernetes Istio Playground


## What is a Service Mesh?

A **Service Mesh** is an infrastructure layer that handles service-to-service communication **securely**, **reliably**, and **observably**, without changing application code. It manages traffic, enforces policies, handles retries/failures, and can encrypt pod-to-pod communication (mTLS).

### Main Steps When Working with a Service Mesh
- Install the Service Mesh (e.g., Istio, Linkerd, Cilium).
- Inject sidecar proxies (e.g., Envoy) into pods.
- Define traffic management policies (e.g., routing, retries, timeouts).
- Enforce security policies (e.g., mTLS encryption).
- Monitor traffic and service health using built-in telemetry.

### What is Istio?
> Istio is an open source service mesh that layers transparently onto existing distributed applications. Istio’s powerful features provide a uniform and more efficient way to secure, connect, and monitor services. Istio is the path to load balancing, service-to-service authentication, and monitoring – with few or no service code changes. 

### Example: Installing and Using Istio

**Install Istio CLI:**
```bash
# https://istio.io/latest/docs/ambient/getting-started/
curl -L https://istio.io/downloadIstio | sh -
cd istio-*
export PATH=$PWD/bin:$PATH
```

**Install Istio in the Cluster (demo profile):**
```bash
# https://istio.io/latest/docs/setup/additional-setup/config-profiles/
istioctl install --set profile=demo -y
```

**Label Namespace for Sidecar Injection:**
```bash
kubectl label namespace default istio-injection=enabled
```

**Deploy a Sample Application:**
```bash
kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml
```

**Verify Sidecar Injection:**
```bash
kubectl get pods
kubectl describe pod <pod-name> | grep "istio-proxy"
```

### Tip
- For CKS exam: focus on how service meshes (like Istio) enable pod-to-pod encryption using mTLS.
- Common mistake: forgetting to label the namespace for automatic sidecar injection.
