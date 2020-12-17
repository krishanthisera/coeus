# Coeus

A sample web application

# Connectivity

# Jenkins Automation 
Jenkins Kubernetes plugin has been associated to execute this pipeline. Prior to the configuration please do ensure that relevent Service Account (associate with a Role and a RoleBinding)  is created for Jenkins to access the cluster resource.

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

Most cases it is recommended to explicitly mention the resource requirement in the manifest. In a scenario where the resource conception of your PODs is unclear. Use Vertical POD Autoscaler (VPA) with [GoldiLocks][]

Note that, It has been identified that utilizing the automation/control workloads on the nodes which also utilizing the production workload is not wise. For instance, some of the Jenkins pod especially in this case the pod which executes the Docker builds is running as the privileged container which could raised the security concerns. On the other hand this separation minimize the concerns of resource outages on the nodes where utilize the production workload. Due to that using Affinities the pods are distributed to their desired worker nodes.

[GoldiLocks]: https://github.com/FairwindsOps/goldilocks