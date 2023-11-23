pipeline {
    agent any
     stages {
            stage('Fetch Pull Request Data') {
                steps {
                    def commitChangeset = sh(returnStdout: true, script: 'git diff-tree --no-commit-id --name-status -r HEAD').trim()
                    echo "Trying again!"
                    echo "Received Body Content: ${commitChangeset}"
                }
            }
    }

     post {
        always {
            echo 'Always'
        }
      }
}

