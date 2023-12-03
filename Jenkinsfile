pipeline{
    agent any

    environment {
        DOCKER_COMPOSE_VERSION = '1.29.2'
        SONARQUBE_SCANNER_VERSION = '4.6.2.2472'
    }
    
    stages{
        stage('Checkout') {
            steps {
                git branch: 'staging', url: 'https://github.com/Big-Zaza/code-challenge'
          }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    sh 'npm install'
                }
            }
        }

        stage('Unit Tests') {
            steps {
                script {
                    sh 'npm test'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    withSonarQubeEnv('SonarQubeServer') {
                        sh "npm install -g sonarqube-scanner@${SONARQUBE_SCANNER_VERSION}"
                        sh 'sonar-scanner'
                    }
                }
            }
        }

        stage('Build and Push Docker Image') {
            steps {
                script {
                    sh 'docker-compose build'
                    withCredentials([string(credentialsId: 'DOCKERHUB_CREDENTIALS', variable: 'DOCKER_LOGIN')]) {
                        sh "docker login -u ${DOCKER_LOGIN_USERNAME} -p ${DOCKER_LOGIN_PASSWORD}"
                    }
                    sh 'docker-compose push'
                }
            }
        }
    }
}
