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

                        sh 'git fetch origin'
                        def changedFiles = sh(script: "git diff-tree -r --no-commit-id --name-only HEAD +refs/heads/master:refs/remotes/origin", returnStdout: true).trim()

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
    def fileList = changedFilesList.tokenize('\n')
    fileList.each { file ->
        if (file.endsWith('.java')) {
            def fileContent = readFile(file.trim())

            if (fileContent.contains('System.out')) {
                echo "File ${file} contains 'sysout'"
            } else {
                echo "File ${file} does not contain 'sysout'"
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

