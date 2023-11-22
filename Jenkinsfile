pipeline {
    agent any

    environment {
        GITHUB_API_URL = 'https://api.github.com'
        GITHUB_TOKEN = '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'.bytes.encodeBase64().toString()
        REPO_OWNER = 'vaibhavnagare'
        REPO_NAME = 'cicdtest'
    }

     stages {
            stage('Fetch Pull Request Data') {
                steps {
                    script {
                        // def payload = readJSON text: env.JSON_PAYLOAD
                        // Extract pull request number from the payload
                        // def pullRequestNumber = payload.pull_request.number
                        def pullRequestNumber = 1
                        println "Starting"
                        def response = getPrBody()
                        println "Got the data"
                        for (entry in response) {
                            if (entry.patch..contains('system.out')) {
                                println 'sysout is present in the file >> $entry.filename'
                            }
                        }

/*                         if (pullRequestData.getResponseCode() == 200) {
                            println "Satus is 200"
                            def pullRequestInfo = pullRequestData.getData()
                            for (entry in pullRequestInfo) {
                                if (entry.patch..contains('system.out')) {
                                    println 'sysout is present in the file >> $entry.filename'
                                }
                            }
                            println "Pull Request Data:"
                            println "Title: ${pullRequestData.title}"
                            println "State: ${pullRequestData.state}"
                        } */
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

def getPrBody(String githubUsername, String githubToken, String repo, String id) {
  def apiUrl = "${env.GITHUB_API_URL}/repos/${env.REPO_OWNER}/${env.REPO_NAME}/pulls/1"
  def text = apiUrl.toURL().getText(requestProperties: ['Authorization': "token ${env.GITHUB_TOKEN}"])
  def json = new JsonSlurper().parseText(text)
  def bodyText = json.body

  return bodyText
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