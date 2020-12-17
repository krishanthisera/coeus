# Coeus
[![Build Pipe][jenkins-badge]][jenkins]

This is a sample Kubernetes deployment associated with Jenkins automation. The payload of this application is a static web application created using [Hugo].

_Note that for the design decisions and design constraints please refer: [docs/SPECS.md]_

## Directory Structure
```sh
.
├── Dockerfile
├── Jenkinsfile
├── LICENSE
├── README.md
├── coeus-intel
...
│   ├── public
...
├── docs
│   └── SPECS.md
├── jenkins-build.yaml
├── k8s
│   ├── helm
│   │   ├── coeus
│   │   │   ├── Chart.yaml
│   │   │   ├── charts
│   │   │   ├── templates
│   │   │   │   ├── NOTES.txt
│   │   │   │   ├── _helpers.tpl
│   │   │   │   ├── deployment.yaml
│   │   │   │   ├── hpa.yaml
│   │   │   │   ├── ingress.yaml
│   │   │   │   ├── service.yaml
│   │   │   │   ├── serviceaccount.yaml
│   │   │   │   └── tests
│   │   │   │       └── test-connection.yaml
│   │   │   └── values.yaml
│   │   └── coeus-0.1.0.tgz
│   └── raw
│       ├── README.md
│       └── coeus.yaml
└── nginx
    └── default.conf
```
## Terminology
Following technologies has been associated to finalize the solution.

| Technology | Purpose | Reference |
| ------ | ------ | ------ |
| Docker | To containerize the application | [Dockerfile] |
| Kubernetes | To orchestrate the containers | [k8s] |
| Helm | To manage the application releases | [k8s/helm] |
| Jenkins | To implement the CI Pipeline | [Jenkinsfile] |

## The Infrastructure
![Infrastructure][coeus-infra]


# How to run
Dependencies:
1. Kubernetes cluster
    - Ingress controller to handle the web traffic (in this case [Nginx-Ingress])
    - [Cert-Manager] to automate the SSL certificates
    - Pre-configured Namespace - optional
    - Kubernetes secret to use when pulling the images from the repo - optional
2. A Domain name (in this case [coeus.bizkt.com.au])
3. [Kubectl] installed
4. [Helm] installed

## Manual Deployment
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
## Deploy with Jenkins
![Coeus Workflow][coeus-workflow]
To automate the deployment with Jenkins
1. Install the Kubernetes plugin for Jenkins
2. Configure the secrets on Jenkins
    - Kubernetes secrets
    - Secret to use with Docker registry
3. Create a Jenkins Pipeline associating the GIT repository
_Note that following Jenkins environment variables can manipulate depending on the context_
```sh
#URL for the Docker Registry. In this case a self hosted Docker registry has been used.
DOCKER_REGISTRY = 'https://shipping.bizkt.com.au:443'
DOCKER_REPO = 'shipping.bizkt.com.au:443/coeus/coeus'
#Jenkins secret for the repository
DOCKER_REPO_CREDENTIAL = 'shipping_admin'

RELEASE_NAME = 'coeus-app' 
RELEASE_NAMESPACE = 'coeus'
APP_VERSION = '0.1'
#Note that, in this case, the build number will be appended to the Docker image's tag
GIT_REPO = 'https://github.com/krishanthisera/coeus.git'
``` 

Jenkins Kubernetes plugin has been associated to execute this pipeline. Prior to the configuration, please do ensure that relevant Service Account (associate with a Role and a RoleBinding)  is created for Jenkins to access the cluster resource.

In most cases, it is recommended to explicitly mention the resource requirements (container's or POD's) in the manifest. In a scenario where the resource consumption of the PODs is unclear, it is recommended to Use Vertical POD Autoscaler (VPA) with [GoldiLocks] to make estimations.

Please see [Design Specification] for the further discussion.

[jenkins-badge]: https://ci.bizkt.com.au/buildStatus/icon?job=coues%2Fmaster

[jenkins-pipeline]: https://i.ibb.co/8YxqbH0/coeus-pipeline.png
[coeus-infra]: https://i.ibb.co/Px8xtbZ/coeus-infra.png
[coeus-workflow]: https://i.ibb.co/r741pXn/coeus-workflow.png

[GoldiLocks]: https://github.com/FairwindsOps/goldilocks
[Hugo]: https://gohugo.io/getting-started/quick-start/
[Cert-Manager]: https://cert-manager.io/docs/
[coeus.bizkt.com.au]: https://coeus.bizkt.com.au/
[Kubectl]: https://kubernetes.io/docs/tasks/tools/install-kubectl/
[Helm]: https://helm.sh/
[helm-dir]: https://github.com/krishanthisera/coeus/tree/master/k8s/helm
[jenkins]: https://ci.bizkt.com.au/job/coues/
[Dockerfile]: https://github.com/krishanthisera/coeus/blob/master/Dockerfile
[k8s/helm]: https://github.com/krishanthisera/coeus/tree/master/k8s/helm 
[k8s]: https://github.com/krishanthisera/coeus/tree/master/k8s 
[Jenkinsfile]: https://github.com/krishanthisera/coeus/blob/master/Jenkinsfile
[jenkins-build.yaml]: https://github.com/krishanthisera/coeus/blob/master/jenkins-build.yaml
[coeus]: https://coeus.bizkt.com.au
[Nginx-Ingress]: https://kubernetes.github.io/ingress-nginx/
[Design Specification]: https://github.com/krishanthisera/coeus/tree/master/docs/SPECS.md 
[docs/SPECS.md]: https://github.com/krishanthisera/coeus/tree/master/docs/SPECS.md 
