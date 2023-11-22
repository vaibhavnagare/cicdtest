pipeline {
    agent any

    stages {
        stage('Verify') {
            steps {
                FILE = getPRChangelog()
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