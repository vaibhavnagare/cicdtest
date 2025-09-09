pipeline {
    agent any
    triggers {
        githubPullRequest() // if using github-pullrequest plugin
    }
    environment {
        LABELS = "" // will hold labels from PR
    }
    stages {
        stage('Extract Labels') {
            steps {
                script {
                    // assuming PR labels are exposed in env or from webhook payload
                    def labelsJson = sh(script: "jq -r '.pull_request.labels[].name' \$GITHUB_EVENT_PATH", returnStdout: true).trim()
                    LABELS = labelsJson.split('\n')
                    echo "Labels on PR: ${LABELS}"
                }
            }
        }
        stage('Run Playwright Tests') {
            steps {
                script {
                    LABELS.each { label ->
                        switch(label.toLowerCase()) {
                            case "surveys":
                                sh "npx playwright test --project=surveys"
                                // OR sh "ant playwright.test.surveys"
                                break
                            case "cx":
                                sh "npx playwright test --project=cx"
                                break
                            case "workforce":
                                sh "npx playwright test --project=workforce"
                                break
                            case "api":
                                sh "npx playwright test --project=api"
                                break
                            default:
                                echo "No matching tests for label: ${label}"
                        }
                    }
                }
            }
        }
    }
}
