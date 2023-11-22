pipeline {
    agent any

    stages {
        stage('Verify') {
            steps {
                currentBranch=`git rev-parse --abbrev-ref HEAD`

                #Get all files modified in git                                                                                                                                \

                FILES=`git diff-tree --no-commit-id --name-only HEAD -r`

                for f in $FILES
                 echo 'Hello World >> $f'
                do
                done

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