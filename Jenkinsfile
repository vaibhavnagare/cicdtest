pipeline {
    agent any
    tools {
        nodejs 'Default Node'
        ant 'Default Ant'
    }
    environment {
        LABELS = ''
    }
    stages {
        stage('Debug') {
            steps {
                echo "Change ID: ${env.CHANGE_ID}, Change Target: ${env.CHANGE_TARGET}, Labels: ${env.CHANGE_LABELS}"
            }
        }
        stage('Checkout PR Branch') {
            steps {
                checkout scm
                sh 'git pull origin pr/${CHANGE_ID}'
            }
        }
        stage('Extract Labels') {
            steps {
                script {
                    if (env.CHANGE_ID) {
                        def response = httpRequest url: "https://api.github.com/repos/vaibhavnagare/cicdtest/pulls/${env.CHANGE_ID}", authentication: 'your-github-credentials-id'
                        def pr = readJSON text: response.content
                        LABELS = pr.labels.collect { it.name }.join(',')
                        echo "Labels on PR: ${LABELS}"
                    } else {
                        echo "No CHANGE_ID found"
                    }
                }
            }
        }
        stage('Ant Build') {
            steps {
                dir('java/console') {
                    sh 'ant build'
                }
            }
        }
        stage('Restart Server') {
            steps {
                sh 'systemctl restart your-server' // Replace with actual command
            }
        }
        stage('Check Server') {
            steps {
                script {
                    def serverUp = sh(script: 'curl -s -o /dev/null -w "%{http_code}" http://localhost:your-port', returnStdout: true).trim() == '200'
                    if (!serverUp) {
                        error 'Server not up'
                    }
                }
            }
        }
        stage('Run Playwright Tests') {
            when {
                expression { LABELS.contains('CX') || LABELS.contains('Surveys') || LABELS.contains('Workforce') || LABELS.contains('API') }
            }
            steps {
                echo "Running Workforce tests..."
                // sh 'npx playwright test tests/workforce-test.spec.js'
            }
        }
    }
    post {
        always {
            githubNotify status: currentBuild.result, description: currentBuild.result == 'SUCCESS' ? 'Tests passed' : 'Tests failed', context: 'ci/jenkins'
        }
    }
}
