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