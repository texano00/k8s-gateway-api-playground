# k8s-gateway-api-playground
Let's play with GatewayAPI!

# Recipe

0. Connect to your GCloud Kubernetes service

1. Install v1-pink version exposed with Ingress API
```
helm upgrade -i -f dummy-helm-values-v1.yaml dummy-release-v1-pink dummy-helm
```

2. Reach it