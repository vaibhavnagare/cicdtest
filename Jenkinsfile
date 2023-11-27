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
                        echo "env.BRANCH_NAME: ${env.BRANCH_NAME}"
                        def branchName = sh(script: 'git rev-parse --abbrev-ref HEAD || git name-rev --name-only HEAD', returnStdout: true).trim()
                        if (branchName == 'HEAD') {
                            def commitID = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
                            echo "Detached HEAD Commit: ${commitID}"

                            branchName = sh(script: 'git branch --contains ${commitID}', returnStdout: true).trim()
                            if (!branchName.contains('HEAD')) {
                                echo "Current Branch: ${branchName}"
                                def changedFilesList = sh(script: 'git diff --name-only ${branchName}', returnStdout: true).trim()
                                checkSysOuts(changedFilesList);
                            } else {
                                def changedFilesList = sh(script: 'git show --pretty="" --name-only ${commitID}', returnStdout: true).trim()
                                checkSysOuts(changedFilesList);
                            }
                        } else {
                            echo "Current Branch: ${branchName}"
                            def tempParam = sh(script: "git rev-parse --abbrev-ref HEAD", returnStdout: true).trim()
                            def parentBranch = sh(script: "git show-branch | grep '*' | grep -v ${tempParam} | head -n1 | sed 's/.*\[\(.*\)\].*/\1/' | sed 's/[\^~].*//'", returnStdout: true).trim()
                            def changedFilesList = sh(script: "git diff --name-only ${branchName} ${parentBranch}", returnStdout: true).trim()
                            checkSysOuts(changedFilesList);
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
