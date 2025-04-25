pipeline {
    agent any

    environment {
        PROJECT_DIR = 'ITE/GCL06/DR416985/Jenkins/node-js-dummy-test'
        BUILD_IMAGE = 'node-build-image'
        TEST_IMAGE = 'node-test-image'
        DEPLOY_IMAGE = 'node-deploy-image'
    }

    stages {
        stage('Clone') {
            steps {
                sh '''
                    rm -rf MDO2025_INO
                    git clone https://github.com/InzynieriaOprogramowaniaAGH/MDO2025_INO.git
                    cd MDO2025_INO
                    git checkout DR416985
                '''
            }
        }

        stage('Build') {
            steps {
                dir("${PROJECT_DIR}") {
                    sh "docker build -t ${BUILD_IMAGE} -f node-build.Dockerfile ."
                }
            }
        }

        stage('Test') {
            steps {
                dir("${PROJECT_DIR}") {
                    sh "docker build -t ${TEST_IMAGE} -f node-test.Dockerfile ."
                    sh "docker run --rm ${TEST_IMAGE}"
                }
            }
        }

        stage('Deploy') {
            steps {
                dir("${PROJECT_DIR}") {
                    sh '''
                        docker rm -f app || true
                        docker build -t ${DEPLOY_IMAGE} -f node-deploy.Dockerfile .
                        docker run -d -p 3000:3000 --name app ${DEPLOY_IMAGE}
                    '''
                }
            }
        }

        stage('Verify') {
            steps {
                sh "curl -v http://localhost:3000 || true"
            }
        }
    }

    post {
        always {
            echo 'Sprzątanie...'
            sh "docker stop app || true"
            sh "docker rm app || true"
        }
    }
}
