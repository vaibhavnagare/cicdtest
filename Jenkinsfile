pipeline {
    agent any
     stages {
            stage('Fetch Pull Request Data') {
                steps {
                    script {
                    echo "Received Body Content: ${env.payload}"
                    echo body.content
                    }
                }
            }
    }

     post {
        always {
            echo 'Always'
        }
      }
}

