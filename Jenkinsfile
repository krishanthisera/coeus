pipeline {
    environment {
        DOCKER_REGISTRY = 'https://shipping.bizkt.com.au:443'
        DOCKER_REPO = 'shipping.bizkt.com.au:443/coeus/coeus'
        DOCKER_REPO_CREDENTIAL = 'shipping_admin'
        HELM_DIR = './k8s/helm/coeus'
        RELEASE_NAME = 'coeus-app' 
        RELEASE_NAMESPACE = 'coeus'
        APP_VERSION = '0.1'
    }
    agent {
        kubernetes {
            yamlFile 'jenkins-build.yaml'
        }
    }
    stages {
        stage("Checkout Source") {
            steps {
                git credentialsId: 'git-fcc', url: 'https://github.com/krishanthisera/coeus.git'
             }
        }
        stage('Docker Build') {
            steps {
                container('docker') {
                    sh "docker build -t ${DOCKER_REPO}:${APP_VERSION}.${BUILD_NUMBER} ."
                }
            }
        }
        stage('Image Shipping') {
            steps {
                container('docker') {
                    withDockerRegistry([credentialsId: "${DOCKER_REPO_CREDENTIAL}", url: "${DOCKER_REGISTRY}"]) {
                        sh "docker push ${DOCKER_REPO}:${APP_VERSION}.${BUILD_NUMBER}"
                    }
                }
            }
        }
        stage('Kubernetes Deploy') {
            steps{
                container('helm') {
                        sh "helm upgrade --install '${RELEASE_NAME}' --set app.image.repository='${DOCKER_REPO}' --set app.image.tag='${APP_VERSION}.${BUILD_NUMBER}' --namespace '${RELEASE_NAMESPACE}' '${HELM_DIR}'"
                }
            }
            post{
                failure{
                    container('helm') {
                            sh "helm install --set app.image.repository='${DOCKER_REPO}' --set app.image.tag='${APP_VERSION}.${BUILD_NUMBER}' '${RELEASE_NAME}' --namespace '${RELEASE_NAMESPACE}' '${HELM_DIR}'" 
                    }
                }
            }    
        }
    }
}