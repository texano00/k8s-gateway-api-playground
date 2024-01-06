# k8s-gateway-api-playground
Let's play with GatewayAPI!

# Recipe

0. Connect to your GKE Kubernetes cluster

1. Install v1-pink version exposed with Ingress API
```
helm upgrade -i -f dummy-helm-values-v1.yaml dummy-release-v1-pink dummy-helm -n development --create-namespace
```
2. Reach it

3. Install v2-green version without Ingress API
```
helm upgrade -i -f dummy-helm-values-v2.yaml dummy-release-v2-green dummy-helm -n development --create-namespace
```

4. Label development namespace

```
kubectl label development shared-public-gateway-access=true
```

5. Cluster operator hat -- install Gateway resource

```
k apply -f gateway-api-stuff/cluster-operator-hat/gateway.yaml
```

6. Developer hat -- install HTTPRoute resource

```
k apply -f gateway-api-stuff/cluster-operator-hat/route-easy.yaml
```

7. Developer hat --  Play with some more comple routes!
```
k apply -f gateway-api-stuff/cluster-operator-hat/route-header-rewrite.yaml
k apply -f gateway-api-stuff/cluster-operator-hat/route-header-path-rewrite.yaml
k apply -f gateway-api-stuff/cluster-operator-hat/route-canary.yaml
k apply -f gateway-api-stuff/cluster-operator-hat/route-blue-green.yaml
```