pipeline {
  agent {
    node {
      label 'ec2'
    }

  }
  stages {
    stage('Checkout') {
      steps {
        git(url: 'https://github.com/lnovara/caturday.git', branch: 'master')
      }
    }
    stage('Docker build') {
      steps {
        script {
          image = docker.build registry + ":$BUILD_NUMBER"
        }

      }
    }
    stage('Docker push') {
      steps {
        script {
          docker.withRegistry("https://837924269795.dkr.ecr.eu-west-1.amazonaws.com", "ecr:eu-west-1:jenkins-ec2-creds") {
            image.push()
          }
        }

      }
    }
    stage('Kubernetes deploy') {
      agent {
        node {
          label 'k8s'
        }

      }
      steps {
        sh 'kustomize edit set imagetag ${registry}:${BUILD_NUMBER}'
        sh 'kustomize build . | kubectl apply -f -'
      }
    }
  }
  environment {
    registry = '837924269795.dkr.ecr.eu-west-1.amazonaws.com/tr_caturday'
    image = ''
  }
}
