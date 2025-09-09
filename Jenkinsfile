stage('Extract Labels') {
    steps {
        script {
            if (env.GITHUB_PR_LABELS) {
                LABELS = env.GITHUB_PR_LABELS.split(',')
                echo "Labels on PR: ${LABELS}"
            } else {
                error "No labels found on PR"
            }
        }
    }
}