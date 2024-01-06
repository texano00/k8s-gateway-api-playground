# k8s-gateway-api-playground
Let's play with GatewayAPI!

# Recipe

0. Connect to your GKE Kubernetes cluster

1. Install v1-pink version exposed with Ingress API
```
helm upgrade -i -f dummy-helm-values-v1.yaml dummy-release-v1-pink dummy-helm -n development --create-namespace
```
2. Edit /etc/hosts file and reach it
```
publicIP my-dummy-colored-app-exposed-by-ingress.net
```

--> http://my-dummy-colored-app-exposed-by-ingress.net


3. Install v2-green version without Ingress API
```
helm upgrade -i -f dummy-helm-values-v2.yaml dummy-release-v2-green dummy-helm -n development --create-namespace
```

4. Label development namespace

```
kubectl label ns development shared-public-gateway-access=true
```

5. Create infrastructure namespace
```
k create ns infrastructure
```

5. Cluster operator hat -- install Gateway resource

```
k apply -f gateway-api-stuff/cluster-operator-hat/gateway.yaml
```

6. Wait to be accepted (it takes few seconds)

Go to "Kubernetes Engine" -> "Gateways, Services & Ingress" -> "Gateways"

7. See the new Load Balancer 

Go to "Cloud Load Balancing"
No rules created yet

6. Developer hat -- install HTTPRoute resource

```
k apply -f gateway-api-stuff/development-hat/route-easy.yaml
```

7.  reach it

--> http://my-dummy-colored-app-exposed-by-gateway-api.net


7. Developer hat --  Play with some more comple routes!
```
k apply -f gateway-api-stuff/development-hat/route-header-rewrite.yaml
k apply -f gateway-api-stuff/development-hat/route-header-path-rewrite.yaml
k apply -f gateway-api-stuff/development-hat/route-canary.yaml
k apply -f gateway-api-stuff/development-hat/route-blue-green.yaml
```