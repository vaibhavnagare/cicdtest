pipeline {
    agent any

    stages {
        stage('Compile') {
            steps {
                echo 'Hello World'
            }
        }

        stage('Run') {
            steps {
                echo 'Hello World'
            }
        }
    }

     post {
        always {
            echo 'Always'
        }
      }
}