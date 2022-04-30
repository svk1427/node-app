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
               sh "docker login -u vamsirecordent -p ${Dockerhubpwd}"
               sh "docker push vamsirecordent/nodeapp:${DOCKER_TAG}"
            }
          }
        }

        stage('deploy to kubernetes') {
            steps {
                sh "chmod +x changeTag.sh"
                sh "./changeTag.sh ${DOCKER_TAG}"
                sshagent(['k8smastermachine']) {
                    sh "scp -o StrictHostKeyChecking=no services.yml node-app-pod.yml ubuntu@3.109.123.87:/home/ubuntu/"
//                     script {
// //                         try {
// //                             sh "ssh ubuntu@3.109.123.87 kubectl apply -f ."
// //                         }catch(error) {
// //                             sh "ssh ubuntu@3.109.123.87 kubectl create -f ."  
// //                         }
//                     }
                }
                
            }
        }
}
}

def getDockerTag() {
    def tag = sh script: 'git rev-parse HEAD', rrturnStdout: true
    return tag
}
