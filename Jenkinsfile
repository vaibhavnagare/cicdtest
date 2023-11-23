pipeline {
    agent any
     stages {
            stage('Fetch Pull Request Data') {
                steps {
                    script {
/*                         def apiUrl = "https://api.github.com/repos/${env.REPO_OWNER}/${env.REPO_NAME}/pulls/1/files"
                        def curlCmd = "curl -H 'Accept: application/vnd.github+json' -H 'Authorization: Bearer ${GITHUB_TOKEN}' ${apiUrl}"
                        def response = sh(script: curlCmd, returnStdout: true).trim()

                         echo "Pull Request Data: ${response}"
                        def pullRequestData = readJSON text: response */

                        echo "Pull Request Data:"
                        echo "Title: ${pullRequestData.title}"
                        echo "State: ${pullRequestData.state}"
                        // Extract other relevant pull request information as needed
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

