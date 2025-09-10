pipeline {
    agent any
    
    // Cancel previous builds for the same PR to avoid duplicates
    options {
        skipDefaultCheckout()
        // Prevent concurrent builds for the same PR
        disableConcurrentBuilds(abortPrevious: true)
    }
    
    // Alternative: Generic Webhook Trigger for label-specific events
    triggers {
        GenericTrigger(
            genericVariables: [
                [key: 'action', value: '$.action'],
                [key: 'label_name', value: '$.label.name'],
                [key: 'pr_number', value: '$.number'],
                [key: 'pr_title', value: '$.pull_request.title'],
                [key: 'pr_labels', value: '$.pull_request.labels[*].name']
            ],
            causeString: 'Triggered by label addition: $label_name on PR #$pr_number',
            token: 'pr-label-webhook-token',
            printContributedVariables: true,
            printPostContent: true,
            regexpFilterText: '$action',
            regexpFilterExpression: '^labeled$'
        )
    }
    
    stages {
        stage('Wait for Label Changes to Settle') {
            when {
                expression { env.action == 'labeled' }
            }
            steps {
                script {
                    // Wait a bit to allow for multiple rapid label changes (unlabel + label)
                    echo "=== API CHECK STAGE ==="
                    echo "Action that triggered this build: ${env.action}"
                    echo "Label '${env.label_name}' was added. Waiting 15 seconds for any simultaneous label changes..."
                    sleep(15)
                    
                    // Fetch the current state of labels directly from GitHub API
                    echo "Fetching current PR state from GitHub API..."
                    
                    try {
                        withCredentials([string(credentialsId: 'github-token', variable: 'GITHUB_TOKEN')]) {
                            def response = sh(
                                script: """
                                    curl -s -H "Authorization: token \${GITHUB_TOKEN}" \\
                                    "https://api.github.com/repos/vaibhavnagare/cicdtest/pulls/${env.pr_number}"
                                """,
                                returnStdout: true
                            ).trim()
                            
                            def jsonSlurper = new groovy.json.JsonSlurper()
                            def prData = jsonSlurper.parseText(response)
                            
                            if (prData.labels) {
                                def actualCurrentLabels = prData.labels.collect { it.name }
                                echo "Actual current PR labels: ${actualCurrentLabels.join(', ')}"
                                
                                // Check if any current labels are product labels
                                def productLabels = ['cx', 'surveys', 'communities', 'workforce', 'api', 'all']
                                def actualProductLabels = actualCurrentLabels.findAll { label ->
                                    productLabels.any { product -> label.toLowerCase().contains(product) }
                                }
                                
                                if (actualProductLabels) {
                                    echo "Final product labels to test: ${actualProductLabels.join(', ')}"
                                    echo "Setting EXECUTE_TESTS = true"
                                    env.EXECUTE_TESTS = 'true'
                                    env.PRODUCT_LABELS_TO_TEST = actualProductLabels.join(',')
                                } else {
                                    echo "No product labels found in final state"
                                    echo "Setting EXECUTE_TESTS = false"
                                    env.EXECUTE_TESTS = 'false'
                                }
                            } else {
                                echo "No labels found on PR"
                                echo "Setting EXECUTE_TESTS = false"
                                env.EXECUTE_TESTS = 'false'
                            }
                        }
                    } catch (Exception e) {
                        echo "Could not fetch current PR state: ${e.message}"
                        echo "Falling back to webhook data"
                        echo "Setting EXECUTE_TESTS = false"
                        env.EXECUTE_TESTS = 'false'
                    }
                    
                    echo "=== FINAL DECISION ==="
                    echo "EXECUTE_TESTS = ${env.EXECUTE_TESTS}"
                    echo "PRODUCT_LABELS_TO_TEST = ${env.PRODUCT_LABELS_TO_TEST}"
                    echo "======================"
                }
            }
        }
        
        stage('Check PR and Labels') {
            steps {
                script {
                    // Debug information - print all webhook variables
                    echo "=== WEBHOOK DEBUG INFO ==="
                    echo "Action: ${env.action}"
                    echo "Label name: ${env.label_name}"
                    echo "PR number: ${env.pr_number}"
                    echo "PR title: ${env.pr_title}"
                    echo "PR labels from webhook: ${env.pr_labels}"
                    echo "=========================="
                    
                    echo "Label '${env.label_name}' was added to PR #${env.pr_number}"
                    echo "PR Title: ${env.pr_title}"
                    echo "This stage shows the webhook trigger info - actual testing decision is made after API check"
                }
            }
        }
        
        stage('Process Product Labels') {
            when {
                expression { 
                    // Only execute if EXECUTE_TESTS is set to true
                    return env.EXECUTE_TESTS == 'true'
                }
            }
            steps {
                script {
                    echo "=== EXECUTING TESTS ==="
                    echo "EXECUTE_TESTS = ${env.EXECUTE_TESTS}"
                    echo "Executing Playwright tests for current PR labels: ${env.PRODUCT_LABELS_TO_TEST}"
                    
                    // Split the product labels and execute tests for each
                    def labelsToTest = env.PRODUCT_LABELS_TO_TEST.split(',').collect { it.trim() }
                    
                    labelsToTest.each { label ->
                        echo "Processing label: ${label}"
                        
                        // Product-specific Playwright test execution
                        switch(label.toLowerCase()) {
                            case ~/.*cx.*/:
                                echo "Running CX Playwright tests..."
                                sh '''
                                    echo "Installing dependencies..."
                                    # npm install
                                    echo "Running CX-specific Playwright tests..."
                                    # npx playwright test tests/cx/ --reporter=html
                                    echo "CX tests completed"
                                '''
                                break
                                
                            case ~/.*surveys.*/:
                                echo "Running Surveys Playwright tests..."
                                sh '''
                                    echo "Installing dependencies..."
                                    # npm install
                                    echo "Running Surveys-specific Playwright tests..."
                                    # npx playwright test tests/surveys/ --reporter=html
                                    echo "Surveys tests completed"
                                '''
                                break
                                
                            case ~/.*communities.*/:
                                echo "Running Communities Playwright tests..."
                                sh '''
                                    echo "Installing dependencies..."
                                    # npm install
                                    echo "Running Communities-specific Playwright tests..."
                                    # npx playwright test tests/communities/ --reporter=html
                                    echo "Communities tests completed"
                                '''
                                break
                                
                            case ~/.*workforce.*/:
                                echo "Running Workforce Playwright tests..."
                                sh '''
                                    echo "Installing dependencies..."
                                    # npm install
                                    echo "Running Workforce-specific Playwright tests..."
                                    # npx playwright test tests/workforce/ --reporter=html
                                    echo "Workforce tests completed"
                                '''
                                break
                                
                            case ~/.*api.*/:
                                echo "Running API Playwright tests..."
                                sh '''
                                    echo "Installing dependencies..."
                                    # npm install
                                    echo "Running API-specific Playwright tests..."
                                    # npx playwright test tests/api/ --reporter=html
                                    echo "API tests completed"
                                '''
                                break
                                
                            case ~/.*all.*/:
                                echo "Running ALL Playwright tests..."
                                sh '''
                                    echo "Installing dependencies..."
                                    # npm install
                                    echo "Running ALL Playwright tests..."
                                    # npx playwright test --reporter=html
                                    echo "All tests completed"
                                '''
                                break
                                
                            default:
                                echo "Unknown product label: ${label}"
                                echo "Skipping test execution for this label"
                        }
                    }
                }
            }
        }
        stage('Publish Test Results') {
            when {
                expression { 
                    // Only publish results if tests were executed
                    return env.EXECUTE_TESTS == 'true'
                }
            }
            steps {
                script {
                    echo "Publishing Playwright test results..."
                    // Uncomment these when you have actual Playwright tests
                    // publishHTML([
                    //     allowMissing: false,
                    //     alwaysLinkToLastBuild: false,
                    //     keepAll: true,
                    //     reportDir: 'playwright-report',
                    //     reportFiles: 'index.html',
                    //     reportName: 'Playwright Test Report'
                    // ])
                    
                    // junit 'test-results/*.xml' // If you generate JUnit XML reports
                    echo "Test results would be published here"
                }
            }
        }
    }
}
