pipeline {
    agent any

    environment {
        APP_NAME = 'node-js-dummy-test'
        IMAGE_NAME = "node-app:${env.BUILD_NUMBER}"
        WORKDIR = 'ITE/GCL06/DR416985/Jenkins/node-js-dummy-test'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                dir("${WORKDIR}") {
                    sh 'docker build -t ${IMAGE_NAME} -f node-build.Dockerfile .'
                }
            }
        }

        stage('Test') {
            steps {
                dir("${WORKDIR}") {
                    sh 'docker build -t ${IMAGE_NAME}-test -f node-test.Dockerfile .'
                    sh 'docker run --rm ${IMAGE_NAME}-test'
                }
            }
        }

        stage('Deploy') {
            steps {
                dir("${WORKDIR}") {
                    sh 'docker build -t ${IMAGE_NAME}-deploy -f node-deploy.Dockerfile .'
                    sh 'docker rm -f app || true'
                    sh 'docker run -d -p 3000:3000 --name app ${IMAGE_NAME}-deploy'
                }
            }
        }

        stage('Verify') {
            steps {
                sh 'sleep 5' // Czekamy na uruchomienie aplikacji
                sh 'curl -f http://localhost:3000'
            }
        }
    }

    post {
        success {
            echo 'Pipeline zakończony sukcesem.'
        }
        failure {
            echo 'Pipeline zakończony niepowodzeniem.'
        }
    }
}
