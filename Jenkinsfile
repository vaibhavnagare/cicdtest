pipeline {
    agent { label 'devops2' }

    options {
        skipDefaultCheckout(true)
        disableConcurrentBuilds()
        timeout(time: 30, unit: 'MINUTES')
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }

    triggers {
        githubPush()
    }

    environment {
        REMOTE_HOST = 'hivelabs2'
        REMOTE_PORT = '25431'
        REMOTE_USER = 'questionpro'
        TEST_SCRIPT = '/home/questionpro/amber/bin/runPlaywrightTest.sh'
        TEST_ENV = 'labs'
        VALID_LABELS = 'CX,Surveys,Workforce,Communities,All'
    }

    stages {
        stage('Check Labels') {
            steps {
                script {
                    echo "=== LABEL VALIDATION ==="

                    if (!env.CHANGE_ID) {
                        echo "Direct push detected - skipping label check"
                        return
                    }

                    def hasValidLabel = checkForValidLabels()
                    if (!hasValidLabel) {
                        echo "No valid product labels found. Valid labels: ${env.VALID_LABELS}"
                        echo "Skipping test execution."
                        currentBuild.result = 'ABORTED'
                        return
                    }

                    echo "Valid product label found - proceeding with tests"
                    env.EXECUTE_TESTS = 'true'
                }
            }
        }

        stage('Execute Tests on Remote Instance') {
            when {
                environment name: 'EXECUTE_TESTS', value: 'true'
            }
            steps {
                script {
                    echo "=== BRANCH ANALYSIS ==="

                    def branchName = getBranchName()
                    echo "Source branch: ${branchName}"
                    echo "PR Number: ${env.CHANGE_ID ?: 'Direct push'}"
                    echo "Target branch: ${env.CHANGE_TARGET ?: 'N/A'}"

                    if (!branchName) {
                        echo "Could not determine branch name. Skipping tests."
                        return
                    }

                    echo "=== EXECUTING TESTS ON REMOTE INSTANCE ==="
                    echo "Branch: ${branchName}"
                    def sshCommand = buildSSHCommand(branchName)
                    try {
                        sh sshCommand
                        echo "Remote test execution completed successfully"
                    } catch (Exception e) {
                        error "Failed to execute remote tests: ${e.getMessage()}"
                    }
                }
            }
        }
    }

    post {
        always {
            script {
                echo "Branch processed: ${env.BRANCH_NAME_TO_PROCESS}"
            }
        }
        success {
            echo "✅ Pipeline completed successfully"
        }
        failure {
            echo "❌ Pipeline failed"
        }
        cleanup {
            cleanWs()
        }
    }
}

// Function to check for valid product labels
def checkForValidLabels() {
    if (!env.CHANGE_ID) {
        return false
    }

    def productLabels = ['cx', 'surveys', 'communities', 'workforce', 'api', 'all']

    try {
        def response = sh(
            script: """
                curl -s -H "Authorization: token \${GITHUB_TOKEN}" \\
                     "https://api.github.com/repos/\${GITHUB_REPO}/pulls/${env.CHANGE_ID}"
            """,
            returnStdout: true
        ).trim()

        def prData = readJSON text: response
        def prLabels = prData.labels.collect { it.name }

        echo "PR Labels: ${prLabels}"

        def hasValidLabel = prLabels.any { label ->
            productLabels.contains(label)
        }

        if (hasValidLabel) {
            def matchingLabels = prLabels.findAll { label ->
                productLabels.contains(label)
            }
            echo "Found valid product labels: ${matchingLabels}"
        }

        return hasValidLabel

    } catch (Exception e) {
        echo "Error checking labels: ${e.getMessage()}"
        echo "Proceeding with tests as fallback"
        return true
    }
}

// Helper function to get branch name
def getBranchName() {
    def branchName = env.CHANGE_BRANCH ?: env.BRANCH_NAME ?: env.GIT_BRANCH
    if (branchName?.startsWith('origin/')) {
        branchName = branchName.replace('origin/', '')
    }
    return branchName
}

// Function to build SSH command
def buildSSHCommand(String branchName) {
    return """
        echo "Connecting to remote instance..."
        sudo ssh -p ${env.REMOTE_PORT} \\
                 -oHostKeyAlgorithms=+ssh-dss \\
                 -oStrictHostKeyChecking=no \\
                 -oConnectTimeout=30 \\
                 root@${env.REMOTE_HOST} \\
        "chmod +x ${env.TEST_SCRIPT} && \\
         chown ${env.REMOTE_USER}:${env.REMOTE_USER} ${env.TEST_SCRIPT} && \\
         su - ${env.REMOTE_USER} -c 'cd /home/questionpro/amber && ${env.TEST_SCRIPT} ${branchName} ${env.TEST_ENV ?: ''}'"
    """
}