pipeline {
    agent any

    environment {
        DOCKER_IMAGE_TAG = "" // Initialize with an empty string
        DOCKERHUB_CREDENTIALS_ID = 'DOCKERHUB_CREDENTIALS'
        DOCKER_USERNAME = 'DOCKERHUB_CREDENTIALS.username'
        DOCKER_PASSWORD = 'DOCKERHUB_CREDENTIALS.password'
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
        stage('Read/Increment Version') {
            steps {
                script {
                    // Generate a version using a fixed prefix and Git commit hash
                    def commitHash = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
                    def version = "v${commitHash}"
                    echo "Generated version: $version"
                    DOCKER_IMAGE_TAG = version
                }
            }
        }

        stage('Build and Push') {
            steps {
                script {
                    // Build and tag the Docker images
                    sh "docker-compose build"
                    sh "docker tag mern-crud bigzaza/mern-crud:v2"
                  //  $DOCKER_IMAGE_NAME:$DOCKER_IMAGE_TAG"
                }
            }
        }
        
        stage('Build and Push Docker Image') {
            steps {
                script { 
                    withCredentials([usernamePassword(credentialsId: 'DOCKERHUB_CREDENTIALS', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh "echo ${DOCKER_PASSWORD} | docker login -u ${DOCKER_USERNAME} --password-stdin docker.io"                    
                    }
                }
             }
        }

        stage('Push image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'DOCKERHUB_CREDENTIALS', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh "docker push bigzaza/mern-crud:v2"
        }
    }
 }              
}            
                    
                //    $DOCKER_IMAGE_NAME:$DOCKER_IMAGE_TAG
            }
            }
        
    

    

