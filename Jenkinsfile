pipeline {
    agent any
    stages {
        stage('Get PR Number and Print Label') {
            steps {
                script {
                    echo "env.BUILD_URL :: ${env.BUILD_URL}" // Keep your debug output
                    // Try to get PR number from webhook payload
                    def prNumber
                    def eventPath = env.GITHUB_EVENT_PATH
                    if (eventPath) {
                        def event = readJSON file: eventPath
                        prNumber = event.number ?: event.pull_request.number
                        echo "PR Number from webhook: ${prNumber}"
                    } else {
                        // Fallback: Use BRANCH_NAME if set (e.g., pr/1)
                        prNumber = env.BRANCH_NAME?.replace('pr/', '') ?: '1' // Default to 1 if not found
                        echo "PR Number from branch name: ${prNumber}"
                    }

                    // Fetch and print labels using the PR number
                    if (prNumber) {
                        def response = httpRequest url: "https://api.github.com/repos/vaibhavnagare/cicdtest/pulls/${prNumber}", authentication: '037682c3-5f53-4f28-99f3-d79e58918c7d'
                        def pr = readJSON text: response.content
                        def labels = pr.labels.collect { it.name }.join(', ')
                        echo "Labels from GitHub PR #${prNumber}: ${labels}"
                    } else {
                        echo "Could not determine PR number"
                    }
                }
            }
        }
    }
}

