pipeline {
    agent any
    
    // Standard pipeline options
    options {
        // Enable automatic checkout
        timeout(time: 30, unit: 'MINUTES')
        // Control build concurrency to limit Docker containers
        throttleJobProperty(
            categories: ['playwright-tests'],
            throttleEnabled: true,
            throttleOption: 'category',
            maxConcurrentPerNode: 4,
            maxConcurrentTotal: 4
        )
    }
    
    // Use GitHub webhook instead of Generic Webhook Trigger
    triggers {
        githubPush()
    }
    
    stages {
        stage('Setup Environment') {
            steps {
                script {
                    echo "=== ENVIRONMENT SETUP ==="
                    echo "Preparing Docker-enabled Jenkins environment..."
                    
                    // Verify Docker availability
                    sh '''
                        echo "=== CHECKING ENVIRONMENT ==="
                        echo "Working directory: $(pwd)"
                        echo "Docker version:"
                        docker --version || echo "Docker not found"
                        echo "Available tools:"
                        which java && java -version || echo "Java not found"
                        which ant && ant -version || echo "Ant not found"
                        echo "========================="
                    '''
                }
            }
        }
        
        stage('Execute in Docker Container') {
            steps {
                script {
                    echo "=== DOCKER CONTAINER EXECUTION ==="
                    
                    // Using Docker-beside-Docker approach with your Docker-enabled Jenkins
                    docker.image('node:18-alpine').inside('-v /var/run/docker.sock:/var/run/docker.sock --user root') {
                        
                        // Install required tools in Alpine container
                        sh '''
                            echo "=== CONTAINER SETUP ==="
                            echo "Container ID: $(hostname)"
                            
                            # Install essential tools
                            apk add --no-cache git curl openjdk11-jre apache-ant
                            
                            # Verify installations
                            node --version
                            npm --version
                            ant -version
                            git --version
                            
                            echo "Environment ready for testing"
                        '''
                        
                        stage('Checkout and Parse Branch') {
                            echo "=== CHECKOUT & PARSING STAGE ==="
                            
                            // Get branch name from Jenkins environment
                            def branchName = env.BRANCH_NAME ?: env.GIT_BRANCH
                            if (branchName && branchName.startsWith('origin/')) {
                                branchName = branchName.replace('origin/', '')
                            }
                            
                            echo "Target branch: ${branchName}"
                            echo "Build trigger: GitHub webhook"
                            echo "Container setup: Docker-beside-Docker"
                            
                            // Verify checkout
                            sh '''
                                echo "Current working directory:"
                                pwd
                                ls -la
                                echo "Git status:"
                                git status || echo "Git not initialized"
                            '''
                            
                            // Store branch name for later use
                            env.TARGET_BRANCH = branchName
                            
                            if (branchName) {
                                // Parse branch name format
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
                                    env.PRODUCT_TO_TEST = foundProducts.join(',')
                                } else {
                                    echo "No product names found in branch name"
                                    env.EXECUTE_TESTS = 'false'
                                }
                            } else {
                                env.EXECUTE_TESTS = 'false'
                            }
                            
                            echo "EXECUTE_TESTS = ${env.EXECUTE_TESTS}"
                            echo "PRODUCT_TO_TEST = ${env.PRODUCT_TO_TEST}"
                        }
                        
                        stage('Execute Tests') {
                            when {
                                expression { return env.EXECUTE_TESTS == 'true' }
                            }
                            script {
                                echo "=== EXECUTING TESTS ==="
                                
                                def productsToTest = env.PRODUCT_TO_TEST.split(',').collect { it.trim() }
                                
                                productsToTest.each { product ->
                                    echo "Processing product: ${product}"
                                    
                                    switch(product.toLowerCase()) {
                                        case 'cx':
                                            sh 'ant playwright.test.cx'
                                            break
                                        case 'surveys':
                                            sh 'ant playwright.test.surveys'
                                            break
                                        case 'communities':
                                            sh 'ant playwright.test.communities'
                                            break
                                        case 'workforce':
                                            sh 'ant playwright.test.workforce'
                                            break
                                        case 'api':
                                            sh 'ant playwright.test.api'
                                            break
                                        case 'all':
                                            sh 'ant playwright.test'
                                            break
                                        default:
                                            echo "Unknown product: ${product}"
                                    }
                                }
                            }
                        }
                    }
                }
            }
        }
    }
    
    post {
        always {
            script {
                echo "=== CLEANUP ==="
                echo "Docker container cleanup will be handled automatically"
                // Publish test results if available
                // publishHTML([...]) // Uncomment when you have test reports
            }
        }
    }
}
