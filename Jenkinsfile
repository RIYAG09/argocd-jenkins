pipeline {
    agent any

    environment {
        IMAGE_NAME = "jenkins-argocd-demo"
        DOCKER_USER = "riya299"  
        TAG = "${BUILD_NUMBER}"
        IMAGE = "${DOCKER_USER}/${IMAGE_NAME}:${TAG}"
        DEPLOYMENT_FILE = "deployment.yaml"
    }

    stages {
        stage('Checkout') {
            steps {
                git credentialsId: 'github-creds', url: 'https://github.com/RIYAG09/argocd-jenkins.git', branch: 'main'
            }
        }

        stage('Build Image') {
            steps {
                sh 'docker build -t $IMAGE .'
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh '''
                        echo $PASS | docker login -u $USER --password-stdin
                        docker push $IMAGE
                    '''
                }
            }
        }

        stage('Update Kubernetes Manifest') {
            steps {
               withCredentials([usernamePassword(credentialsId: 'github-creds', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                    sh """
                      git config --global user.name RIYAG09
                      git config --global user.email riya.csit09@gmail.com
                      git remote set-url origin https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/RIYAG09/argocd-jenkins.git
                      git add deployment.yaml
                      git commit -m "Update image to ${env.IMAGE_TAG}"
                      git push origin main
                    """
                }
            }
        }
    }
}
