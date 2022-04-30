pipeline {
    agent any
    environment {
        DOCKER_TAG = getDockerTag()
    }
    stages{
        stage('checkout code') {
            steps {
               git credentialsId: 'git-credentials', url: 'https://github.com/svk1427/node-app.git'
            }
        }
     
        stage('BUild Docker image') {
            steps {
                sh "docker build . -t vamsirecordent/nodeapp:${DOCKER_TAG}"
            }
        }

        stage('Push Image to dokcer hub'){
            steps {
           withCredentials([string(credentialsId: 'dockerhub_pwd', variable: 'Dockerhubpwd')]) {
               sh "docker login -u vamsirecordent ${Dockerhubpwd}"
               sh "docker push vamsirecordent/nodeapp:${DOCKER_TAG}"
            }
          }
        }
}
}

def getDockerTag() {
    def tag = sh script: 'git rev-parse HEAD', rrturnStdout: true
    return tag
}
