pipeline {
    agent none
    options {
        ansiColor('xterm')
        buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '10')
        timeout(time: 10, unit: 'SECONDS')
        timestamps

    }
    triggers {
        pollSCM ('H * * * *')

    }
    parameters {
        choice choices: ['Build', 'Test', 'Compile', 'Deploy', 'Produ'], description: ' Creating multiple options to make efficient one', name: 'Choice'
        string defaultValue: 'main', description: 'Asking the user to select valid branch', name: 'MY-BRANCH'
    }
    environment {
        NAME = 'Pipeline'
        TYPE = 'Declarative'

    }
    stages {
        stage ('STAGE1') {
            agent {
                label 'slave1'
            }
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'class_git', url: 'https://github.com/TejasKH13/today_task_23sep.git']])
                echo "Running stage1"
                sh 'pwd'
                echo "${env.NAME}"
                echo "${env.TYPE}"
            }
        stage ('STAGE2') {
            environment {
                WH_STAGE = 'STAGE1'
            }
            agent {
                label 'slave2'
            }
            parallel{
                stage ('sub-stage1'){
                    steps {
                        echo "${env.WH_STAGE}"
                        echo "Running sub stage1"
                        sh 'date'

                    }
                stage ('sub-stage2') {
                    steps {
                        echo "${env.WH_STAGE}"
                        echo "Running sub stage2"
                        echo ""
                    }
                }
                }
            }
        stage ('stage3') {
            steps {
                script {
                    try {
                        echo "Running stage 3"
                        sh 'exit 1'

                        env.STAGE_3_STATUS = "SUCCESS"
                    }catch (Exception e) {
                        echo "Got some error and handling: ${e.message}"
                        env.STAGE_3_STATUS = "FAILED"

                    }
                }
            }
        }
        stage ('STAGE4') {
            when {
                expression {
                    env.STAGE_3_STATUS =='FAILED'
                }
            }
            steps {
                echo "executing the stage4"
                sh 'uptime'
            }
        }  
        }
        
        }
    }
}