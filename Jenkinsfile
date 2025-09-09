pipeline {
    agent any
    stages {
        stage('Get PR Number') {
            steps {
                script {
                    // Try common environment variables for PR number
                    def prNumber = env.CHANGE_ID ?: env.ghprbPullId ?: 'Not a PR build'
                    echo "PR Number: ${prNumber}"
                }
            }
        }
    }
}
