pipeline {
  agent {
    label 'go'
  }
     
  environment {
    REGISTRY = 'docker.io'
    // Docker Hub 用户名
    DOCKERHUB_USERNAME = 'Your Docker Hub username'
    APP_NAME = 'devops-go-sample'
    // ‘dockerhub’ 即您在 KubeSphere 控制台上创建的 Docker Hub 凭证 ID
    DOCKERHUB_CREDENTIAL = credentials('dockerhub')
    // 您在 KubeSphere 控制台上创建的 kubeconfig 凭证 ID
    KUBECONFIG_CREDENTIAL_ID = 'kubeconfig'
    // 您企业空间中的多集群项目名称
    MULTI_CLUSTER_PROJECT_NAME = 'demo-multi-cluster'
    // 您用来部署应用的成员集群名称
    // 本教程中，应用部署在主集群和一个成员集群上
    // 若需要部署在多个成员集群上, 请编辑 manifest/multi-cluster-deploy.yaml
    MEMBER_CLUSTER_NAME = 'Your Member Cluster name'
  }  
     
  stages {
    stage('docker login') {
      steps {
        container('go') {
          sh 'echo $DOCKERHUB_CREDENTIAL_PSW  | docker login -u $DOCKERHUB_CREDENTIAL_USR --password-stdin'
        }
      }
    }
       
    stage('build & push') {
      steps {
        container('go') {
          sh 'git clone https://github.com/yuswift/devops-go-sample.git'
          sh 'cd devops-go-sample && docker build -t $REGISTRY/$DOCKERHUB_USERNAME/$APP_NAME .'
          sh 'docker push $REGISTRY/$DOCKERHUB_USERNAME/$APP_NAME'
        }
      }
    }
       
    stage('deploy app to multi cluster') {
      steps {
         container('go') {
            withCredentials([
              kubeconfigFile(
                credentialsId: env.KUBECONFIG_CREDENTIAL_ID,
                variable: 'KUBECONFIG')
              ]) {
              sh 'envsubst < devops-go-sample/manifest/multi-cluster-deploy.yaml | kubectl apply -f -'
              }
           }
        }
      }
    }
  }
