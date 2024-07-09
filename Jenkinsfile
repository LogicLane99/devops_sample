pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'vishal8266/nextjs-app'
        DOCKER_CREDENTIALS_ID = '31c18687-84ff-4865-82f3-b80a6d088921'
    }
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/LogicLane99/devops_sample.git'
            }
        }
        stage('Install Dependencies') {
            steps {
                script {
                    docker.image('node:14').inside {
                        sh 'npm install'
                    }
                }
            }
        }
        stage('Build') {
            steps {
                script {
                    docker.image('node:14').inside {
                        sh 'npm run build'
                    }
                }
            }
        }
        stage('Docker Build & Push') {
            steps {
                script {
                    docker.withRegistry('', "${DOCKER_CREDENTIALS_ID}") {
                        def app = docker.build("${env.DOCKER_IMAGE}:${env.BUILD_NUMBER}")
                        app.push()
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    sh '''
                    docker stop nextjs-app || true
                    docker rm nextjs-app || true
                    docker run -d -p 3000:3000 --name nextjs-app ${DOCKER_IMAGE}:${BUILD_NUMBER}
                    '''
                }
            }
        }
    }
    post {
        always {
            cleanWs()
        }
        success {
            echo 'Deployment succeeded!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}
