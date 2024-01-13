# k8s-gateway-api-playground
Let's play with GatewayAPI!

- [k8s-gateway-api-playground](#k8s-gateway-api-playground)
- [Experience path](#experience-path)
  - [Primer](#primer)
  - [Ingress exercise](#ingress-exercise)
  - [Gateway API exercise](#gateway-api-exercise)

# Experience path
## Primer
1. Connect to GKE Kubernetes cluster

## Ingress exercise
1. First exercise, use Ingress API to expose in a basic way the "blue" application

    ```
    +-----------------+          +---------------------+          +------+
    | External Source |    =>    | Load Balancer       |    =>    | blue |
    +-----------------+          +---------------------+          +------+
                                                           100%
                                                
    ```

2. Install v1-blue version exposed with Ingress API
    ```
    helm upgrade -i -f dummy-helm-values-v1.yaml dummy-release-v1-blue dummy-helm -n development --create-namespace
    ```

3. Wait the GKE Cloud Loadbalancer provisioning on web console and copy the URL

4. Edit /etc/hosts file and reach it
    ```
    [publicIP] my-dummy-colored-app-exposed-by-ingress.net
    ```

    Reach it --> http://my-dummy-colored-app-exposed-by-ingress.net

5. Limitations\
   Ex. \
   Q: How can I add some http url rewrite or header manipulation?\
   A: You can't!

## Gateway API exercise
1. Install v2-green version without any Ingresses
    ```
    helm upgrade -i -f dummy-helm-values-v2.yaml dummy-release-v2-green dummy-helm -n development --create-namespace
    ```

2. Label development namespace

    ```
    kubectl label ns development shared-public-gateway-access=true
    ```

3. Create infrastructure namespace
    ```
    k create ns infrastructure
    ```

4. 🧢 **Cluster operator hat** -- install Gateway resource
Note that GaytewayClass is already provided by the cloud provider (in this case by GKE)

    ```
    k apply -f gateway-api-stuff/cluster-operator-hat/gateway.yaml
    ```

5. Wait the Gateway to be accepted (it takes few seconds)

   Go to "Kubernetes Engine" -> "Gateways, Services & Ingress" -> "Gateways"

6. Let's see the new Load Balancer 

    Go to "Cloud Load Balancing"    
    Note that no rules created yet

7. 🧢 **Developer operator hat** -- install HTTPRoute resource

    ```
    k apply -f gateway-api-stuff/development-hat/route-easy.yaml
    ```

8. Wait the rules to be synced

    Go to "Cloud Load Balancing"    
    Some rules should come up

9. Retrieve the new cloud load balancer URL, edit /etc/hosts file and reach it
    ```
    [publicIP] my-dummy-colored-app-exposed-by-gateway-api.net
    ```

    Reach it --> http://my-dummy-colored-app-exposed-by-gateway-api.net


10. 🧢 **Developer operator hat** --  Play with some more complex routes!

    **10.a** **add header** to the request
    ```
    +-----------------+          +------------------------+          +------+
    | External Source |    =>    | Api Gateway / Balancer |    =>    | blue |
    +-----------------+          +------------------------+          +------+
                                                                100%
                                                                custom-header:<value>             
                                                
    ```
    ```
    k apply -f gateway-api-stuff/development-hat/route-header-rewrite.yaml
    ```

    **10.b** **path rewrite**
    ```
    +-----------------+          +------------------------+          +------+
    | External Source |    =>    | Api Gateway / Balancer |    =>    | blue |
    +-----------------+          +------------------------+          +------+
                                                                100%
                                                                custom-header:<value>       
                                                                / --> /custom-path      
                                                
    ```
    ```
    k apply -f gateway-api-stuff/development-hat/route-header-path-rewrite.yaml
    ```
    
    **10.c canary** deploy
    ```
    +-----------------+          +------------------------+             +------+
    | External Source |    =>    | Api Gateway / Balancer |    (no)  => | blue |
    +-----------------+          +------------------------+             +------+
                                `traffic:test` header there?       
                                                                        +-------+
                                                               (yes) => | green |
                                                                        +-------+
 
    ```
    ```
    k apply -f gateway-api-stuff/development-hat/route-canary.yaml
    ```
    
    **10.d blue green** deploy
    ```
    +-----------------+          +------------------------+             +------+
    | External Source |    =>    | Api Gateway / Balancer |    50%  =>  | blue |
    +-----------------+          +------------------------+             +------+

                                                                        +-------+
                                                               50%   => | green |
                                                                        +-------+
 
    ```

    ```
    k apply -f gateway-api-stuff/development-hat/route-blue-green.yaml
    ```
    