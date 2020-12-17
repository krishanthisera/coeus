# Kubernetes
Primarily, to orchestrate the containers, to maintain the resiliency and also to scale the application deployment 'Digital Ocean Kubernetes Engine' has been used.

By enabling the **autoscaling**, using [/k8s/helm/coeus/values.yaml], Horizontal POD Autoscaler (HPA) can be enable.

_Please note that [metric server] should be running on the K8s cluster to HPA to perform._
```sh
autoscaling:
  enabled: true
```
Both data and management payload (PODs) scheduling have been separated using **affinities**. Optionally, 'Kubernetes taint & tolerations' can be used depending on the context.

## Kubernetes Constraints
It has been assumed that the K8s cluster is secure enough for the payload. If it's must to ensure the security of 'service to service' (POD to POD) communication, it is recommended to follow secured approach such as Mutual TLS(mTLS). Service meshes like [Linkerd], [Istio], [Consul] provide the mTLS features.

_Note that In the context of service meshes, cost and complexity of the implementation are significant._

As it obvious Kubernetes secrets are not encrypted. In this context, a couple of Kubernetes secrets have been used (implicitly or explicitly)
1. Docker registry credentials
2. Cert-manager SSL Certificates
3. Secret associate with Kubernetes Service accounts (SA) - Service account token Secrets and etc.
    Ex. to retrieve the Secrete associated to 'Jenkins' SA:
    `$kubectl get secret $(kubectl get sa jenkins -n jenkins -o jsonpath={.secrets[0].name}) -n jenkins -o jsonpath={.data.token} | base64 --decode)`

# Helm
To package and to simplify the solution, a Helm chart has been created. Using the values defined in the [/k8s/helm/coeus/values.yaml] file, deployment can be customized. Further, for the readability, a static(raw) manifest file has been provided in [/k8s/raw/coeus.yaml]

# Private Docker Registry
A docker registry which has been hosted on the same Kubernetes cluster under [shipping.bizkt.com.au:443] used to store the docker images. Especially this implementation is backed by [Digital Ocean Space] which is an object storage equivalent to AWS S3.

This implementation consider to be cost effective in a situation where it has more than one private repository to be stored on the 'Docker Hub'.

# Nginx ingress and Cert-manager
Nginx ingress controller has been used to manage (Load Balance) the web traffic to the services in the cluster. Parallel to the Ingress controller, the cert-manager implementation has been used to automated the SSL certificate management. In this manner, without any human intervention certificate will be rotated.

# Jenkins Automation
Jenkins has been used to stream line the deployment process. Especially, Jenkins server is hosted in the same Kubernetes cluster which reduce the overhead of the maintenance.

Jenkins Kubernetes plugin has been used to build/ship the images and to deploy the Kubernetes resources. [jenkins-build.yaml] describes the template of the Jenkins agent POD which executes the Jenkins jobs on the Kubernetes cluster.

Note that, It has been identified that utilizing the automation/control workloads on the nodes which also utilize the production workloads is risky. For instance, some of the Jenkins PODs, in this case the POD which executes the Docker builds is running as a privileged container which could raised security concerns. Due to that, using **node affinities** PODs are scheduled in manner that they respect this separation. On the other hand this separation minimize the concerns of resource outages on the nodes which utilize the production workload.

```sh
affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: payload
            operator: In
            values:
            - automation
            - operation 
            - management
```
[jenkins-pipeline]: https://i.ibb.co/8YxqbH0/coeus-pipeline.png

[/k8s/helm/coeus/values.yaml]: https://github.com/krishanthisera/coeus/blob/master/k8s/helm/coeus/values.yaml
[metric server]: https://github.com/kubernetes-sigs/metrics-server
[Linkerd]: https://linkerd.io/
[Istio]: https://istio.io/
[Consul]: https://www.consul.io/
[/k8s/raw/coeus.yaml]: https://github.com/krishanthisera/coeus/blob/master/k8s/raw/coeus.yaml
[shipping.bizkt.com.au:443]: shipping.bizkt.com.au:443
[Digital Ocean Space]: https://zhipping.sgp1.digitaloceanspaces.com 
[jenkins-build.yaml]: https://github.com/krishanthisera/coeus/blob/master/jenkins-build.yaml