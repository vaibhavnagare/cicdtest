pipeline {
    agent any

    
    // Standard pipeline options
    options {
        // Prevents Jenkins from auto-checking out the branch
        skipDefaultCheckout()           
        // Prevent concurrent builds on same branch
        disableConcurrentBuilds()
    }
    
    // Use GitHub webhook
    triggers {
        githubPush()
    }
    
    stages {
        stage('Extract Product from Branch Name') {
            steps {
                script {
                    echo "=== BRANCH ANALYSIS ==="
                    
                    // Get actual branch name (handle PR case)
                    def branchName = env.CHANGE_BRANCH ?: env.BRANCH_NAME ?: env.GIT_BRANCH
                    if (branchName && branchName.startsWith('origin/')) {
                        branchName = branchName.replace('origin/', '')
                    }
                    
                    echo "Source branch: ${branchName}"
                    echo "PR Number: ${env.CHANGE_ID ?: 'Direct push'}"
                    echo "Target branch: ${env.CHANGE_TARGET ?: 'N/A'}"
                    
                    if (branchName) {
                        // Parse branch name format: developername-builddate-branchname-productname-typeofissue-issue-details
                        def branchParts = branchName.split('-')
                        echo "Branch parts: ${branchParts.join(', ')}"
                        
                        // Find product name in branch parts
                        def productLabels = ['cx', 'surveys', 'communities', 'workforce', 'api', 'all']
                        def foundProducts = []
                        
                        branchParts.each { part ->
                            def partLower = part.toLowerCase()
                            productLabels.each { product ->
                                if (partLower.contains(product)) {
                                    foundProducts.add(product)
                                }
                            }
                        }
                        
                        foundProducts = foundProducts.unique()
                        
                        if (foundProducts) {
                            echo "Product(s) detected: ${foundProducts.join(', ')}"
                            env.EXECUTE_TESTS = 'true'
                            env.PRODUCT_TO_TEST = foundProducts[0] // Take first product found
                            env.BRANCH_NAME_TO_PROCESS = branchName
                        } else {
                            echo "No product names found in branch name"
                            echo "Expected format: developername-builddate-branchname-productname-typeofissue-issue-details"
                            echo "Valid product names: ${productLabels.join(', ')}"
                            env.EXECUTE_TESTS = 'false'
                        }
                    } else {
                        echo "Could not determine branch name"
                        env.EXECUTE_TESTS = 'false'
                    }
                    
                    echo "=== RESULT ==="
                    echo "EXECUTE_TESTS = ${env.EXECUTE_TESTS}"
                    echo "PRODUCT_TO_TEST = ${env.PRODUCT_TO_TEST}"
                    echo "BRANCH_NAME_TO_PROCESS = ${env.BRANCH_NAME_TO_PROCESS}"
                    echo "==============="
                }
            }
        }
        
        stage('Execute Tests on Remote Instance') {
            when {
                expression { return env.EXECUTE_TESTS == 'true' }
            }
            steps {
                script {
                    echo "=== EXECUTING TESTS ON REMOTE INSTANCE ==="
                    echo "Branch: ${env.BRANCH_NAME_TO_PROCESS}"
                    echo "Product: ${env.PRODUCT_TO_TEST}"
                    
                    // Execute shell script on remote instance
                    sh """
                        echo "Executing script on local dev instance..."
                        
                        # Execute the script on local machine outside Docker
                        # Method 1: Direct execution if script is mounted/accessible
                        /amber/bin/runPlaywrightTest.sh ${env.BRANCH_NAME_TO_PROCESS} ${env.PRODUCT_TO_TEST}
                        
                        # Method 2: Alternative paths (uncomment and modify as needed)
                        # /home/vaibhavnagare/amber/bin/runPlaywrightTest.sh ${env.BRANCH_NAME_TO_PROCESS} ${env.PRODUCT_TO_TEST}
                        # /opt/amber/bin/runPlaywrightTest.sh ${env.BRANCH_NAME_TO_PROCESS} ${env.PRODUCT_TO_TEST}
                        
                        echo "Script execution completed"
                    """
                    // sh """
                    //     echo "Connecting to remote instance..."
                        
                    //     # Option 1: Using SSH to execute script on remote machine
                    //     # ssh user@your-remote-instance.com '/amber/bin/runPlaywrightTest.sh ${env.BRANCH_NAME_TO_PROCESS} ${env.PRODUCT_TO_TEST}'
                        
                    //     # Option 2: Using remote script execution with parameters
                    //     # curl -X POST "http://your-remote-instance/execute-tests" \
                    //     #      -H "Content-Type: application/json" \
                    //     #      -d '{"branch": "${env.BRANCH_NAME_TO_PROCESS}", "product": "${env.PRODUCT_TO_TEST}"}'
                    //
                    //     # Option 3: Direct SSH command execution
                    //     # ssh -o StrictHostKeyChecking=no user@remote-host << 'EOF'
                    //     # cd /path/to/your/project
                    //     # git pull origin master
                    //     # git checkout ${env.BRANCH_NAME_TO_PROCESS}
                    //     # # Clean build steps here
                    //     # # Restart server steps here  
                    //     # ant playwright.test.${env.PRODUCT_TO_TEST}
                    //     # EOF
                        
                    //     # For now, just echo the command that would be executed
                        echo "Would execute: test-script.sh ${env.BRANCH_NAME_TO_PROCESS} ${env.PRODUCT_TO_TEST}"
                        echo "Remote script should:"
                        echo "1. Git pull latest changes"
                        echo "2. Checkout to branch: ${env.BRANCH_NAME_TO_PROCESS}"
                        echo "3. Clean build"
                        echo "4. Restart server"
                        echo "5. Execute: ant playwright.test.${env.PRODUCT_TO_TEST}"
                    // """
                }
            }
        }
    }
    
    post {
        always {
            script {
                echo "=== BUILD COMPLETE ==="
                if (env.EXECUTE_TESTS == 'true') {
                    echo "Tests executed for product: ${env.PRODUCT_TO_TEST}"
                    echo "Branch processed: ${env.BRANCH_NAME_TO_PROCESS}"
                } else {
                    echo "No tests executed - branch name did not match expected format"
                }
                // Add test result publishing here if needed
                // publishHTML([...])
            }
        }
        success {
            echo "✅ Pipeline completed successfully"
        }
        failure {
            echo "❌ Pipeline failed"
        }
    }
}

