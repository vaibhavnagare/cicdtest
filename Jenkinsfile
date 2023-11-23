pipeline {
    agent any

    environment {
        GITHUB_API_URL = 'https://api.github.com'
        GITHUB_TOKEN = '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'
        REPO_OWNER = 'vaibhavnagare'
        REPO_NAME = 'cicdtest'
    }

     stages {
            stage('Fetch Pull Request Data') {
                steps {
                    script {
                        def apiUrl = "https://api.github.com/repos/${env.REPO_OWNER}/${env.REPO_NAME}/pulls/1/files"
                        def curlCmd = "curl -H 'Accept: application/vnd.github+json' -H 'Authorization: Bearer ${GITHUB_TOKEN}' ${apiUrl}"
                        def response = sh(script: curlCmd, returnStdout: true).trim()

                         echo "Pull Request Data: ${response}"
                        def pullRequestData = readJSON text: response
                        echo "Pull Request Data:"
                        echo "Title: ${pullRequestData.title}"
                        echo "State: ${pullRequestData.state}"
                        // Extract other relevant pull request information as needed
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

@NonCPS
List<String> getChangedFilesList(){
    def changedFiles = []
    for (changeLogSet in currentBuild.changeSets) {
        for (entry in changeLogSet.getItems()){
            changedFiles.addAll(entry.affectedPaths)
        }
    }
    return changedFiles
}

@NonCPS
String getGitcommitID(){
    gitCommitID = " "
    for ( changeLogSet in currentBuild.changeSets){
        for (entry in changeLogSet.getItems()){
            gitCommitID = entry.commitId
        }
    }
    return gitCommitID
}

@NonCPS
String getCommitMessage(){
    commitMessage = " "
    for ( changeLogSet in currentBuild.changeSets){
        for (entry in changeLogSet.getItems()){
            commitMessage = entry.msg
        }
    }
    return commitMessage
}
