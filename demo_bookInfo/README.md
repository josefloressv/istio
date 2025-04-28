# BookInfo demo

https://istio.io/latest/docs/examples/bookinfo/

Label the namespace that will host the application with istio-injection=enabled:

```bash
kubectl label namespace default istio-injection=enabled
```

Deploy the application

```bash
kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml

# veryfy
k get all

# Simple test from the pod
kubectl exec "$(kubectl get pod -l app=ratings -o jsonpath='{.items[0].metadata.name}')" -c ratings -- curl -sS productpage:9080/productpage | grep -o "<title>.*</title>"
```

Create an Istio Gateway using the following command:

```bash
kubectl apply -f samples/bookinfo/networking/bookinfo-gateway.yaml

# verify
kubectl get gateway
kubectl get virtualservice
```

Install only istio-ingressgateway

```bash
istioctl install \
  --set components.ingressGateways[0].enabled=true \
  --set components.ingressGateways[0].name=istio-ingressgateway
```

Set GATEWAY_URL:

```bash
export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].nodePort}')
export INGRESS_HOST=$(kubectl get po -l istio=ingressgateway -n istio-system -o jsonpath='{.items[0].status.hostIP}')
export GATEWAY_URL=$INGRESS_HOST:$INGRESS_PORT
```

Confirm the app is accessible from outside the cluste

```bash
curl -s "http://${GATEWAY_URL}/productpage" | grep -o "<title>.*</title>"
```
*From KodeKloud PlayGround use the option View port > $INGRESS_HOST*


