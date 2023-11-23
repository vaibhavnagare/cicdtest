pipeline {
    agent any
     stages {
            stage('Fetch Pull Request Data') {
                steps {
                    echo "Received Body Content: ${payload}"
                }
            }
    }

     post {
        always {
            echo 'Always'
        }
      }
}

