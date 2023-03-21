pipeline {
    agent {
        node {
            label 'molecule-virtualbox'
        }
    }
    options {
        skipStagesAfterUnstable()
    }
    environment {
        PATH = "$HOME/.local/bin:$PATH"
    }

    stages {
        stage('Test'){
            agent { label 'molecule-virtualbox' }
            steps {
                script {
                    sh "VAGRANT_DEFAULT_PROVIDER=virtualbox molecule test"
                }
            }

        post {
            success {
                updateGitlabCommitStatus name: 'Test', state: 'success'
            }
            failure {
                updateGitlabCommitStatus name: 'Test', state: 'failed'
            }
        } 
        }
    }
    post {
        success {
            updateGitlabCommitStatus name: 'Pipeline', state: 'success'
        }
        failure {
            updateGitlabCommitStatus name: 'Pipeline', state: 'failed'
        }
    } 
}
