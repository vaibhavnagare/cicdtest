pipeline {
    agent any

     stages {
            stage('Retrieve PR File Paths') {
                steps {
                    script {
                        List<String> changes = getPRChangelog()
                        for (filePath in changes){
                            println ("Changed file list: " + filePath)
                        }

                        String gitCommitId = getGitcommitID()
                        println("GIT CommitID: " + gitCommitID)

                        String gitCommitMessage = getCommitMessage()
                        println("GIT CommitMessage: " + gitCommitMessage)
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

@NonCPS
List<String> getChangedFilesList(){
    def changedFiles = []
    for ( changeLogSet in currentBuild.changeSets){
        for (entry in changeLogSet.getItems()){
            println("GIT CommitMessage: " + entry.affectedPaths)
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