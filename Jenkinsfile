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
                        def apiUrl = "https://api.github.com/repos/${env.REPO_OWNER}/${env.REPO_NAME}/pulls/1"
                        def curlCmd = "curl -H 'Authorization: token ${GITHUB_TOKEN}' -H 'Accept: application/vnd.github.v3+json' ${apiUrl}"
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