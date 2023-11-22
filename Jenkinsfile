pipeline {
    agent any

     stages {
            stage('Retrieve PR File Paths') {
                steps {
                    script {
                        // Get the Git URL of the repository
                        def gitUrl = sh(returnStdout: true, script: 'git config --get remote.origin.url').trim()

                        // Extract repository owner's name and repository name from the Git URL
                        def gitParts = gitUrl.tokenize(':')[1].tokenize('/')
                        def ownerName = gitParts[0]
                        def repoName = gitParts[1].replace('.git', '')

                        // Get the pull request number from the environment variable
                        def prNumber = env.CHANGE_ID.toInteger()

                        def gitHubContext = github

                        def pullRequest = gitHubContext.getPullRequest(owner: ownerName, repository: repoName, number: prNumber)

                        // Get the changed files in the pull request
                        def changedFiles = gitHubContext.getPullRequestFiles(owner: ownerName, repository: repoName, number: prNumber)

                        // Print the file paths
                        for (file in changedFiles) {
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