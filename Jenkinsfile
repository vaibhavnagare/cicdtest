pipeline {
    agent any

     stages {
            stage('Retrieve PR File Paths') {
                steps {
                    script {
                        List<String> changes = getChangedFilesList()
                        println ("Changed file list: " + changes)

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