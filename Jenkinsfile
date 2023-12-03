pipeline{
    agent any
    stages{
        stage('checkout') {
            steps {
                git branch: 'staging', url: 'https://github.com/Big-Zaza/code-challenge'
          }
        }
        stage('update') {
            steps {
                echo "This is just an update to tell you guys that we're pushing"
            }
        }
    }
}
