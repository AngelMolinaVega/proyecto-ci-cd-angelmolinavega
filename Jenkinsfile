pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'angelmolinavega'
        APP_NAME = 'proyecto-ci-cd-angelmolinavega'
    }

    stages {
        stage('Clone') {
            steps {
                checkout scm
            }
        }

        stage('Test') {
            steps {
                sh 'pip install flask pytest --break-system-packages'
                sh 'pytest'
            }
        }

        stage('Build Image') {
            steps {
                sh "docker build -t ${DOCKERHUB_USER}/${APP_NAME}:${env.BUILD_ID} ."
                sh "docker build -t ${DOCKERHUB_USER}/${APP_NAME}:latest ."
            }
        }

        stage('DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh "echo ${PASS} | docker login -u ${USER} --password-stdin"
                    sh "docker push ${DOCKERHUB_USER}/${APP_NAME}:${env.BUILD_ID}"
                    sh "docker push ${DOCKERHUB_USER}/${APP_NAME}:latest"
                }
            }
        }

        stage('Deploy') {
            steps {
                sh 'kubectl apply -f deployment.yaml'
                sh 'kubectl apply -f service.yaml'
            }
        }
    }
}
