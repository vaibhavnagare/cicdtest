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

                        def apiUrl = 'https://api.github.com/repos/vaibhavnagare/cicdtest' // Replace placeholders with actual values
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
                                // Process each pull request data as needed
                                println "Pull Request Title: ${pullRequest.title}"
                                println "Pull Request URL: ${pullRequest.html_url}"
                                // Add more processing or actions here
                            }
                        } else {
                            println "Failed to fetch pull requests. Status code: ${response.status}"
                            println "Response: ${response.content}"
                        }

                       /*  def response = httpRequest acceptType: 'APPLICATION_JSON', contentType: 'APPLICATION_JSON',
                                                   httpMode: 'GET',
                                                   Authorization: 'Bearer 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'
                                                   url: "https://api.github.com/repos/vaibhavnagare/cicdtest/pulls/1/files" */
                        echo "Changed Files: ${response}"

/*                         def gitUrl = 'https://github.com/vaibhavnagare/cicdtest.git' // Replace with your repository URL
                        checkout([$class: 'GitSCM', branches: [[name: '*//*  *//* ${env.CHANGE_BRANCH}']], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'CheckoutOption', timeout: 10]], submoduleCfg: [], userRemoteConfigs: [[url: gitUrl]]])

                        def changedFiles = sh(script: "git diff --name-only origin/${env.CHANGE_BRANCH} origin/${env.CHANGE_TARGET}", returnStdout: true).trim()
                        echo "Changed Files: ${changedFiles}" */

/*
                        pullRequest.setCredentials('vaibhavdnagare', 'Vaibhav20006!')
                        for (commitFile in pullRequest.files) {
                            echo "SHA: ${commitFile.sha} File Name: ${commitFile.filename} Status: ${commitFile.status}"
                        }
 */

/*                         sh 'git fetch origin'
                        def changedFiles = sh(script: "git diff --name-only origin/${CHANGE_TARGET} origin/${CHANGE_BRANCH}", returnStdout: true).trim()
                        // Process changedFiles as needed
                        echo "Changed Files: ${changedFiles}"

                        def changedFilesList = sh(script: "git diff origin/${env.CHANGE_BRANCH} origin/${env.CHANGE_TARGET}", returnStdout: true).trim()
                        checkSysOuts(changedFilesList); */
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
