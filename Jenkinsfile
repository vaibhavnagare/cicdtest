pipeline {
    agent any
    stages {
        stage('Print GitHub Label') {
            steps {
                script {
                    if (env.CHANGE_ID) {
                        def response = httpRequest url: "https://api.github.com/repos/vaibhavnagare/cicdtest/pulls/${env.CHANGE_ID}", authentication: 'your-github-credentials-id'
                        def pr = readJSON text: response.content
                        def labels = pr.labels.collect { it.name }.join(', ')
                        echo "Labels from GitHub PR: ${labels}"
                    } else {
                        echo "No CHANGE_ID found"
                    }
                }
            }
        }
    }
}
