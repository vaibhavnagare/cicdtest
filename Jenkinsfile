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
                        echo 'Pulling...' + env.BRANCH_NAME
                        def branchName = sh(script: 'git branch -a', returnStdout: true).trim()
                        echo "Current Branch: ${branchName}"
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

