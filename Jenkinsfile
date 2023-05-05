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
        stage('Test I') {
            parallel {
                stage('Test Ubuntu 20.04'){
                    agent { label 'molecule-virtualbox' }
                    steps {
                        catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                            retry(5) {
                                // retry code block
                                script {
                                    sh "MOLECULE_DISTRO=geerlingguy/ubuntu2004 molecule test --parallel"
                                }
                            }
                        }
                    }

                post {
                    success {
                        updateGitlabCommitStatus name: 'Test Ubuntu 20.04', state: 'success'
                    }
                    failure {
                        updateGitlabCommitStatus name: 'Test Ubuntu 20.04', state: 'failed'
                    }
                } 
                }
                stage('Test Rocky Linux 8'){
                    agent { label 'molecule-virtualbox' }
                    steps {
                        catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                            retry(5) {
                                // retry code block
                                script {
                                    sh "MOLECULE_DISTRO=geerlingguy/rockylinux8 molecule test --parallel"
                                }
                            }
                        }
                    }
                    post {
                        success {
                            updateGitlabCommitStatus name: 'Test Rocky Linux 8', state: 'success'
                        }
                        failure {
                            updateGitlabCommitStatus name: 'Test Rocky Linux 8', state: 'failed'
                        }
                    } 
                }
            }
        }
    }
        
    post {
        success {
            updateGitlabCommitStatus name: 'Pipeline', state: 'success'
            script {
                withCredentials([string(credentialsId: "jenkinsChannelChatid", variable: "CHAT_ID")]) {
                    telegramSend(message: "✅\nJob: ${env.JOB_NAME}\nBuild: ${env.BUILD_NUMBER}\nDuration: ${currentBuild.durationString}\nResult: SUCCESS", chatId:CHAT_ID)
                }
            }
        }
        failure {
            updateGitlabCommitStatus name: 'Pipeline', state: 'failed'
            script {
                withCredentials([string(credentialsId: "jenkinsChannelChatid", variable: "CHAT_ID")]) {
                    telegramSend(message: "🤬\nJob: ${env.JOB_NAME}\nBuild: ${env.BUILD_NUMBER}\nDuration: ${currentBuild.durationString}\nResult: FAILURE", chatId:CHAT_ID)
                }
            }
        }
    } 
}
