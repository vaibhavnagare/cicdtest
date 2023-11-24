pipeline {
    agent any

     stages {
            stage('Retrieve PR File Paths') {
                steps {
                    script {
                        echo "Test :: ${env.CHANGE_BRANCH}"
/*                         def commonAncestor = sh(script: 'git merge-base current_branch other_branch', returnStdout: true).trim()
                        echo "Common Ancestor Commit: ${commonAncestor}"

                        def commitHistory = sh(script: "git log --oneline --graph --decorate ${commonAncestor}..current_branch", returnStdout: true).trim()
                        echo "Commit History Since Common Ancestor:"
                        echo commitHistory */
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