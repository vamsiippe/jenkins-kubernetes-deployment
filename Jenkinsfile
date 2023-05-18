pipeline {
  environment {
    dockerimagename =  "vamsisnikky/react-app:${BUILD_NUMBER}"
    dockerImage = ""
  }
  agent {
    docker {
      image 'abhishekf5/maven-abhishek-docker-agent:v1'
      args '--user root -v /var/run/docker.sock:/var/run/docker.sock' // mount Docker socket to access the host's Docker daemon
    }
  }
  stages {
    stage('Checkout Source') {
      steps {
        git branch: 'main', credentialsId: '17811133-d29c-46bc-808a-e40eaf7a82d5', url: 'https://github.com/vamsiippe/jenkins-kubernetes-deployment.git'
      }
    }
    stage('Build image') {
      steps{
        script {
          dockerImage = docker.build dockerimagename
        }
      }
    }
    stage('Pushing Image') {
      environment {
               registryCredential = 'dockerhub-credentials'
           }
      steps{
        script {
          sh 'echo passed'

          //docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
          //  dockerImage.push()
          //}
        }
      }
    }
    
    stage('Update Deployment File') {
        environment {
            GIT_REPO_NAME = "jenkins-kubernetes-deployment-manifests"
            GIT_USER_NAME = "vamsiippe"
        }
        steps {
            withCredentials([string(credentialsId: 'github', variable: 'GITHUB_TOKEN')]) {
                sh '''
                    git clone https://github.com/vamsiippe/${GIT_REPO_NAME}
                    git config user.email "vamsi.ippe@innovasolutions.com"
                    git config user.name "vamsi.ippe"
                    BUILD_NUMBER=${BUILD_NUMBER}
                    cd jenkins-kubernetes-deployment-manifests
                    sed -i "s/replaceImageTag/${BUILD_NUMBER}/g" deployment.yml
                    git add deployment.yml
                    git commit -m "Update deployment image to version ${BUILD_NUMBER}"
                    git push https://${GITHUB_TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:main
                '''
            }
        }
    }
  }
}