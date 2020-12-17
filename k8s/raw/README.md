# Purpose
This an static representation of the Kubernetes resources. To deploy in the production please refer the [Helm implementation].
# Dependencies
Following implementations should be available in your Kubernetes cluster in order to deploy the application successfully.
1. Ingress controller
2. Kubernetes Cert-manager 
3. A Kubernetes namespace call `coeus`
4. A docker-secret to pull the image from you private registry

Note that, a private registry (`shipping.bizkt.com.au:443`) which has been hosted on the same Kubernetes cluster has been used as the primary docker registry.

# Manual Deployment
To deploy the application manually,
1. Clone the git repository
`$git clone https://github.com/krishanthisera/coeus.git`
2. cd into the raw directory
`$cd k8s/raw`
3. Deploy the application using the manifest file given
`$kubectl apply -f k8s/raw/coeus.yaml`

_Note that, since the values are hardcoded to manifest file including the namespace, please do manipulate them depending on the environment_

# Node Affinity
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

[Helm implementation]: https://github.com/krishanthisera/coeus/tree/master/k8s/helm