# Purpose
To package and to simplify the solution, this Helm chart has been created. Using the values defined in the [/k8s/helm/coeus/values.yaml] file, deployment can be customized.

# Dependencies
Following implementations should be available in your Kubernetes cluster in order to deploy the application successfully.
1. Ingress controller
2. Kubernetes Certmanager 
3. A Kubernetes namespace call `coeus`
4. A docker-secret to pull the image from you private registry

# Manual Deployment
To deploy the application manually,
1. Clone the git repository
`$git clone https://github.com/krishanthisera/coeus.git`
2. cd into the helm directory
`$cd k8s/helm/coeus`
3. Install the release
    ```sh
    $helm install -n <namespace> <release-name> .
    $#If ingress required to be disabled
    $helm install -n <namespace> <release-name> . --set ingress.enabled=false
    $helm ls -n coeus
    NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
    coeus-app       coeus           2               2020-12-17 04:27:27.718309171 +0000 UTC deployed        coeus-0.1.0     1.16.0  
    ```

    [/k8s/helm/coeus/values.yaml]:  https://github.com/krishanthisera/coeus/blob/master/k8s/helm/coeus/values.yaml