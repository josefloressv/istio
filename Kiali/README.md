
# Kiali

## Quick Concept Summary

Kiali is the observability console for Istio service mesh. It provides a visual graph of microservices, real-time traffic flow, health status, and troubleshooting tools for Istio workloads.

## Main Steps when Working with Kiali

- Install Istio and enable telemetry (metrics, tracing).
- Deploy Kiali alongside Istio (istioctl or Helm).
- Access Kiali UI (port-forwarding or Ingress).
- Monitor services, traffic flow, validations, and configuration issues.
- Troubleshoot service-to-service communication and visualize mTLS settings.



# Kiali + Bookinfo Quick Lab

This guide helps you quickly deploy the Bookinfo app, visualize it using Kiali, simulate traffic, and troubleshoot microservice failures.

## 1. Install Istio (demo profile)

```bash
istioctl install --set profile=demo -y
```

## 2. Install Addons (Prometheus, Jaeger, Grafana)

You have multiple options to install the addons needed by Kiali:

- **Option 1 (Recommended for CKS and quick labs):**
  Use the `samples/addons/` folder provided in the Istio package you downloaded:

```bash
cd ~/istio-1.25.2/samples/addons/
kubectl apply -f prometheus.yaml
kubectl apply -f jaeger.yaml
kubectl apply -f grafana.yaml
```

- **Option 2:**
  Apply addons directly from the Istio GitHub repository (from release-1.22 branch):

```bash
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.22/samples/addons/prometheus.yaml
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.22/samples/addons/jaeger.yaml
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.22/samples/addons/grafana.yaml
```

- **Option 3:**
  Install Prometheus, Grafana, Jaeger using Helm charts (advanced/customized deployments).

## 3. Install Kiali

Since Istio v1.24, Kiali must be installed separately. Install it using Helm:

```bash
helm repo add kiali https://kiali.org/helm-charts
helm repo update
helm install kiali-server kiali/kiali-server --namespace istio-system --set auth.strategy="anonymous"
```

## Notes about Addon Installation and Changes

- In newer versions (Istio 1.24+), Kiali is no longer bundled inside `istioctl install`. It must be deployed separately.
- Prometheus is mandatory for Kiali to display metrics.
- Jaeger and Grafana are optional but enhance Kiali's functionality (tracing and dashboard links).
- It is recommended to use the local `samples/addons/` YAMLs shipped with your Istio version to avoid incompatibilities.

## 2. Deploy Bookinfo Application

Enable sidecar injection and deploy Bookinfo:

```bash
kubectl label namespace default istio-injection=enabled
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.22/samples/bookinfo/platform/kube/bookinfo.yaml
```

## 3. Expose Bookinfo via Istio Gateway

```bash
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.22/samples/bookinfo/networking/bookinfo-gateway.yaml
```

Retrieve external IP:

```bash
kubectl get svc istio-ingressgateway -n istio-system
export GATEWAY_URL=<your-external-ip>
```

Test access:

```bash
curl -s http://$GATEWAY_URL/productpage
```

## 4. Access Kiali Dashboard

Port-forward Kiali:

```bash
kubectl port-forward svc/kiali 20001:20001 -n istio-system
```

Access it at: [http://localhost:20001](http://localhost:20001)

## 5. Generate Traffic for Visualization

```bash
while true; do curl -s http://$GATEWAY_URL/productpage > /dev/null; sleep 0.5; done
```

## 6. Simulate a Microservice Failure

Option 1: Delete reviews Pods

```bash
kubectl delete pod -l app=reviews
```

Option 2: Scale down reviews service

```bash
kubectl scale deployment reviews-v1 --replicas=0
```

Check the Kiali graph for traffic errors and service degradation.

## Notes

- Ensure traffic is being generated; otherwise, Kiali graphs may appear empty.
- Bookinfo app must be deployed in a namespace with Istio sidecar injection enabled.
- Use Kiali validations and graph legends to troubleshoot issues like mTLS lock, traffic failures, and routing problems.
