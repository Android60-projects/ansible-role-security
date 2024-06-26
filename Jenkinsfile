void setBuildStatus(String context, String message, String state) {
  step([
      $class: "GitHubCommitStatusSetter",
      reposSource: [$class: "ManuallyEnteredRepositorySource", url: "https://github.com/Android60/ansible-role-security"],
      contextSource: [$class: "ManuallyEnteredCommitContextSource", context: context],
      errorHandlers: [[$class: "ChangingBuildStatusErrorHandler", result: "UNSTABLE"]],
      statusResultSource: [ $class: "ConditionalStatusResultSource", results: [[$class: "AnyBuildResult", message: message, state: state]] ]
  ]);
}

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
                        setBuildStatus(STAGE_NAME, "Build succeeded", "SUCCESS");
                        updateGitlabCommitStatus name: 'Test Ubuntu 20.04', state: 'success'
                    }
                    failure {
                        setBuildStatus(STAGE_NAME, "Build failed", "FAILURE");
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
                            setBuildStatus(STAGE_NAME, "Build succeeded", "SUCCESS");
                            updateGitlabCommitStatus name: 'Test Rocky Linux 8', state: 'success'
                        }
                        failure {
                            setBuildStatus(STAGE_NAME, "Build failed", "FAILURE");
                            updateGitlabCommitStatus name: 'Test Rocky Linux 8', state: 'failed'
                        }
                    } 
                }
            }
        }
    }
        
    post {
        success {
            setBuildStatus("Molecule test", "Build succeeded", "SUCCESS");
            script {
                withCredentials([string(credentialsId: "jenkinsChannelChatid", variable: "CHAT_ID")]) {
                    telegramSend(message: "✅\nJob: ${env.JOB_NAME}\nBuild: ${env.BUILD_NUMBER}\nDuration: ${currentBuild.durationString}\nResult: SUCCESS", chatId:CHAT_ID)
                }
            }
        }
        failure {
            setBuildStatus("Molecule test", "Build failed", "FAILURE");
            script {
                withCredentials([string(credentialsId: "jenkinsChannelChatid", variable: "CHAT_ID")]) {
                    telegramSend(message: "🤬\nJob: ${env.JOB_NAME}\nBuild: ${env.BUILD_NUMBER}\nDuration: ${currentBuild.durationString}\nResult: FAILURE", chatId:CHAT_ID)
                }
            }
        }
    } 
}