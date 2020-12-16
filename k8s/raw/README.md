#Purpose
This an static representation of the Kubernetes resources. To deploy in the production please refer the Helm implementation.
#Dependencies
Following implementations should be available in your Kubernetes cluster in order to deploy the application successfully.
1. Ingress controller
2. Kubernetes Certmanager 
3. A Kubernetes namespace call `coeus`
4. A docker-secret to pull the image from you private registry

Note that, a private registry (`shipping.bizkt.com.au:443`) which has been hosted on the same Kubernetes cluster has been used as the primary docker registry.


#Node Affinity
Node Affinities has been used to isolate the application workload from management services.
```sh
affinity:
      nodeAffinity:
        requiredDuringSchedulingIgnoredDuringExecution:
          nodeSelectorTerms:
          - matchExpressions:
            - key: payload
              operator: NotIn
              values:
              - automation
              - operation
              - management 
```