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
                        echo "env.CHANGE_ID: ${env.CHANGE_ID}"
                        echo "env.BUILD_NUMBER: ${env.BUILD_NUMBER}"
                         echo "PULL_REQUEST is ${env.PULL_REQUEST}"
                        echo "env.BUILD_URL: ${env.BUILD_URL}"
                        echo "env.BRANCH_NAME: ${env.BRANCH_NAME}"
                        echo "env.GIT_BRANCH: ${env.GIT_BRANCH}"
                        echo "env.CHANGE_BRANCH: ${env.CHANGE_BRANCH}"
                        echo "env.CHANGE_URL: ${env.CHANGE_URL}"
                        echo "env.CHANGE_TARGET: ${env.CHANGE_TARGET}"
                        def diffURL = "${env.CHANGE_URL}.diff"
                        echo "diffURL ${diffURL}"

                        def apiUrl = 'https://api.github.com/repos/vaibhavnagare/cicdtest/pulls' // Replace placeholders with actual values
                        def accessToken = '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' // Replace with your GitHub Personal Access Token

                        def response = httpRequest(
                            acceptType: 'APPLICATION_JSON',
                            contentType: 'APPLICATION_JSON',
                            httpMode: 'GET',
                            url: apiUrl,
                            headers: [
                                Authorization: "Bearer $accessToken"
                            ]
                        )

                        if (response.status == 200) {
                            println "Response: ${response.content}"
                            def jsonResponse = new groovy.json.JsonSlurper().parseText(response.content)
                            jsonResponse.each { pullRequest ->
                                if (pullRequest.filename.endsWith('.java')) {
                                    println "Checking file :: ${pullRequest.filename}"
                                    if (pullRequest.patch.contains("System.out")) {
                                        error "File ${file} contains 'system.out'."
                                    }
                                }
                            }
                        } else {
                            println "Failed to fetch pull requests. Status code: ${response.status}"
                            println "Response: ${response.content}"
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

def checkSysOuts(changedFilesList) {
    def fileList = changedFilesList.readLines()
    fileList.each { file ->
        if (file.endsWith('.java')) {
            def fileContent = sh(script: "git show origin/${env.CHANGE_BRANCH}:${file}", returnStdout: true).trim()
            if (fileContent.contains("System.out")) {
                error "File ${file} contains 'system.out'."
            }
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
