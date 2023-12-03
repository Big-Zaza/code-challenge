pipeline{
    agent any

    environment {
        DOCKER_COMPOSE_VERSION = '1.29.2'
    }

  //  tools {
      //  nodejs 'NodeJS 21.3.0'
        // Use the SonarQube Scanner tool
      //  SonarqubeScanner 'SonarQube Scanner 5.0.1.3006'
  //  }

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
/*
        stage('SonarQube Analysis') {
            steps {
                script {
                    // Run SonarQube analysis using the configured SonarQube Scanner
                    withSonarQubeEnv('SonarCloud') {
                        sh 'sonar-scanner'
                    }
                    }
                }
            }
*/
        stage('Debugging') {
            steps {
                script {
                    sh 'echo $PATH'
                    sh 'docker-compose --version'
                }
            }
        }
        
        stage('Build and Push Docker Image') {
            steps {
                script {
                    sh 'docker-compose build'
                    withCredentials([usernamePassword(credentialsId: 'DOCKERHUB_CREDENTIALS', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh "docker login -u ${DOCKER_LOGIN_USERNAME} -p ${DOCKER_LOGIN_PASSWORD}"
                    }
                    sh 'docker-compose push'
                }
            }
        }
    }
}  
    

