pipeline {
    agent any
     stages {
            stage('Fetch Pull Request Data') {
                steps {
                    script {
                // Accessing headers
                    def headers = httpRequest([returnHeaders: true])
                    echo "Received Headers:"
                    headers.getHeaders().each { header ->
                        echo "${header.key}: ${header.value}"
                    }

                    // Accessing body content (limited access)
                    def body = httpRequest([httpMode: 'GET', url: headers.getHeader('X-Github-Event')])
                    echo "Received Body Content:"
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

