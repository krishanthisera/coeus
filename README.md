# coeus

A sample web application

# Connectivity


[CiBizkt]: <>
[ShippingBizkt]: <>
[AppBizkt]: <>Name:                   docker-registry
Namespace:              shipping
CreationTimestamp:      Sun, 13 Sep 2020 17:57:57 +0930
Labels:                 app=docker-registry
                        app.kubernetes.io/managed-by=Helm
                        chart=docker-registry-1.9.4
                        heritage=Helm
                        release=docker-registry
Annotations:            deployment.kubernetes.io/revision: 2
                        meta.helm.sh/release-name: docker-registry
                        meta.helm.sh/release-namespace: shipping
Selector:               app=docker-registry,release=docker-registry
Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        5
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:       app=docker-registry
                release=docker-registry
  Annotations:  checksum/config: c99bb016eb280d8a0195307400df7b878be6f707f12c3a8ad76046a69aba3767
  Containers:
   docker-registry:
    Image:      bizkt/registry:dev
    Port:       5000/TCP
    Host Port:  0/TCP
    Command:
      /bin/registry
      serve
      /etc/docker/registry/config.yml
    Liveness:   http-get http://:5000/ delay=0s timeout=1s period=10s #success=1 #failure=3
    Readiness:  http-get http://:5000/ delay=0s timeout=1s period=10s #success=1 #failure=3
    Environment:
      REGISTRY_AUTH:                       htpasswd
      REGISTRY_AUTH_HTPASSWD_REALM:        Registry Realm
      REGISTRY_AUTH_HTPASSWD_PATH:         /auth/htpasswd
      REGISTRY_HTTP_SECRET:                <set to the key 'haSharedSecret' in secret 'docker-registry-secret'>  Optional: false
      REGISTRY_STORAGE_S3_ACCESSKEY:       <set to the key 's3AccessKey' in secret 'docker-registry-secret'>     Optional: false
      REGISTRY_STORAGE_S3_SECRETKEY:       <set to the key 's3SecretKey' in secret 'docker-registry-secret'>     Optional: false
      REGISTRY_STORAGE_S3_REGION:          sgp1
      REGISTRY_STORAGE_S3_REGIONENDPOINT:  sgp1.digitaloceanspaces.com
      REGISTRY_STORAGE_S3_BUCKET:          zhipping
      REGISTRY_STORAGE_S3_SECURE:          true
    Mounts:
      /auth from auth (ro)
      /etc/docker/registry from docker-registry-config (rw)
  Volumes:
   auth:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  docker-registry-secret
    Optional:    false
   docker-registry-config:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      docker-registry-config
    Optional:  false
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Progressing    True    NewReplicaSetAvailable
  Available      True    MinimumReplicasAvailable
OldReplicaSets:  <none>
NewReplicaSet:   docker-registry-6fb9fd7697 (1/1 replicas created)
Events:          <none>
