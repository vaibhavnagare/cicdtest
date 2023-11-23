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
                    def apiUrl = "${env.GITHUB_API_URL}/repos/${env.REPO_OWNER}/${env.REPO_NAME}/pulls/1/files"
                    def response = httpRequest(
                        url: apiUrl,
                        authentication: env.GITHUB_TOKEN,
                        httpMode: 'GET'
                    )

                    if (response.status == 200) {
                        def modifiedFiles = readJSON text: response.content
                        echo "Modified Files in Pull Request:"
                        modifiedFiles.each { file ->
                            echo file.filename
                        }
                    } else {
                        echo "Failed to fetch modified files. Status code: ${response.status}"
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

