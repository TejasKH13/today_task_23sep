pipeline {
    agent none

    options {
        ansiColor('xterm')
        buildDiscarder(logRotator(
            artifactDaysToKeepStr: '',
            artifactNumToKeepStr: '',
            daysToKeepStr: '',
            numToKeepStr: '10'
        ))
        timestamps()
    }

    triggers {
        pollSCM('H * * * *')
    }

    parameters {
        choice(
            choices: ['Build', 'Test', 'Compile', 'Deploy', 'Produ'],
            description: 'Creating multiple options to make efficient one',
            name: 'Choice'
        )
        string(
            defaultValue: 'main',
            description: 'Asking the user to select valid branch',
            name: 'MY-BRANCH'
        )
    }

    environment {
        NAME = 'Pipeline'
        TYPE = 'Declarative'
    }

    stages {
        stage('STAGE1') {
            agent {
                label 'slave1'
            }
            steps {
                checkout scmGit(
                    branches: [[name: '*/main']],
                    extensions: [],
                    userRemoteConfigs: [[
                        credentialsId: 'class_git',
                        url: 'https://github.com/TejasKH13/today_task_23sep.git'
                    ]]
                )
                echo "Running stage1"
                sh 'pwd'
                echo "${env.NAME}"
                echo "${env.TYPE}"
            }
        }

        stage('STAGE2') {
            
            environment {
                WH_STAGE = 'STAGE1'
            }
            parallel {
                stage('sub-stage1') {
                    agent {
                        label 'slave2'
                    }
                    steps {
                        echo "${env.WH_STAGE}"
                        echo "Running sub stage1"
                        sh 'date'
                    }
                }

                stage('sub-stage2') {
                    agent {
                        label 'slave2'
                    }
                    steps {
                        echo "${env.WH_STAGE}"
                        echo "Running sub stage2"
                        echo ""
                    }
                }
            }
        }

        stage('stage3') {
            agent {
                label 'slave1'
            }

            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    echo "Executing the stage3"
                    sh 'exit 1'
                }
            }
        }

        stage('STAGE4') {
            agent {
                label 'slave2'
            }

            when {
                expression {
                    env.stageResult == 'FAILURE'
                }
            }

            steps {
                echo "executing the stage4"
                sh 'uptime'
            }
        }
    }
}
