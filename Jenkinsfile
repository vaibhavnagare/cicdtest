pipeline {
    agent any
    environment {
        GITHUB_API_URL = 'https://api.github.com'
        GITHUB_TOKEN = credentials('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')
        REPO_OWNER = 'vaibhavnagare'
        REPO_NAME = 'cicdtest'
    }
     stages {
            stage('Fetch Pull Request Data') {
                steps {
                    script {
                        def payload = readJSON text: env.JSON_PAYLOAD // Assuming payload is available in JSON format
                        // Extract pull request number from the payload
                        def pullRequestNumber = payload.pull_request.number


                        def apiEndpoint = "${env.GITHUB_API_URL}/repos/${env.REPO_OWNER}/${env.REPO_NAME}/pulls/${pullRequestNumber}"
                        def response = httpRequest(
                            url: apiEndpoint,
                            authentication: env.GITHUB_TOKEN,
                            httpMode: 'GET'
                        )

                        if (response.status == 200) {
                            def pullRequestData = readJSON text: response.content
                            for (entry in pullRequestData) {
                                if (entry.patch..contains('system.out')) {
                                    echo 'sysout is present in the file >> $entry.filename'
                                }
                            }
                            println "Pull Request Data:"
                            println "Title: ${pullRequestData.title}"
                            println "State: ${pullRequestData.state}"
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