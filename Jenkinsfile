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
        stage('Read/Increment Version') {
            steps {
                script {
                    def versionFile = 'version.txt'
                    def currentVersion = readFile(versionFile).trim()
                    
                    if (currentVersion == '') {
                        currentVersion = '1'
                    } else {
                        currentVersion = currentVersion.toInteger() + 1
                    }

                    DOCKER_IMAGE_TAG = "v${currentVersion}"
                    writeFile file: versionFile, text: currentVersion.toString()
                }
            }
        }

        stage('Build and Push') {
            steps {
                script {
                    // Build and tag the Docker images
                    sh "docker-compose build"
                    sh "docker tag your-service:latest $DOCKER_IMAGE_NAME:$DOCKER_IMAGE_TAG"
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
                    sh "docker push $DOCKER_IMAGE_NAME:$DOCKER_IMAGE_TAG"
                }
            }
        }
    }
}  
    

