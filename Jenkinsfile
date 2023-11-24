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
                    def branchName = sh(script: 'git rev-parse --abbrev-ref HEAD || git name-rev --name-only HEAD', returnStdout: true).trim()
                    if (branchName == 'HEAD') {
                        def commitID = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
                        echo "Detached HEAD Commit: ${commitID}"
                        branchName = sh(script: 'git branch --contains ' commitID, returnStdout: true).trim()
                         echo "Current Branch: ${branchName}"
                    } else {
                        echo "Current Branch: ${branchName}"
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

