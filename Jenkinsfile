pipeline {
    agent any

     stages {
            stage('Retrieve PR File Paths') {
                steps {
                    script {
                     echo 'I am here 1'
                        // Get the Git URL of the repository
                        def gitUrl = sh(returnStdout: true, script: 'git config --get remote.origin.url').trim()
                        echo 'I am here 2'
                        // Extract repository owner's name and repository name from the Git URL
                        def gitParts = gitUrl.tokenize(':')[1].tokenize('/')
                        def ownerName = gitParts[0]
                        def repoName = gitParts[1].replace('.git', '')

                        echo 'I am here 3'
                        // Get the pull request number from the environment variable

                        echo "CHANGE_ID : ${env.CHANGE_ID}"
                        def prNumber = env.CHANGE_ID.toInteger()
                        def gitHubContext = github

                        echo 'I am here 4 $prNumber >> $gitHubContext'

                        def pullRequest = gitHubContext.getPullRequest(owner: ownerName, repository: repoName, number: prNumber)
                        echo 'I am here 5'
                        // Get the changed files in the pull request
                        def changedFiles = gitHubContext.getPullRequestFiles(owner: ownerName, repository: repoName, number: prNumber)
                        echo 'I am here'
                        // Print the file paths
                        for (file in changedFiles) {
                        echo 'Vaibhav'
                            println("Changed file: ${file.filename}")
                        }
                    }
                }
            }
    }

     post {
        always {
            echo 'Always'
        }
      }
}

/**
 * Compares the current branch and target branch and extract the changed files.
 *
 * @return the PR changed files.
 */
def getPRChangelog() {
    return sh(
            script: "git --no-pager diff origin/${params.target} --name-only",
            returnStdout: true
    ).split('\n')
}

