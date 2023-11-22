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
                        println("CHANGE_URL: ${env.CHANGE_URL}")
                        println("CHANGE_URL: ${env.CHANGE_URL_1}")
                        // Extract pull request number from CHANGE_URL or CHANGE_URL_1
                        def prNumber = getPullRequestNumberFromURL(env.CHANGE_URL ?: env.CHANGE_URL_1)

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

def isPullRequest() {
    try {
        return pullRequest != null
    } catch(MissingPropertyException e) {
        return false
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

def getPullRequestNumberFromURL(url) {
    // Extract the pull request number from the URL
    def matcher = (url =~ /pull\/(\d+)/)
    if (matcher.matches()) {
        return matcher[0][1].toInteger()
    } else {
        error "Could not extract pull request number from URL: ${url}"
    }
}