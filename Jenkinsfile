pipeline {
    agent any
     stages {
            stage('Fetch Pull Request Data') {
                steps {
                    script {
                    // Accessing body content (limited access)

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
