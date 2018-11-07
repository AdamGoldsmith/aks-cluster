#!/usr/bin/env groovy

pipeline {

    agent { label "master" }

    options {
        // timestamps
        timestamps()
        // abort if running for stupid long time
        timeout(time: 3, unit: 'HOURS')
    }
    
    environment {
        AZURE_SUBSCRIPTION_ID = credentials('az_subscription_id')
        AZURE_CLIENT_ID = credentials('az_client_id')
        AZURE_SECRET = credentials('az_secret')
        AZURE_TENANT = credentials('az_tenant')
    }

    parameters {

        string(name: 'input_name',
            defaultValue: '',
            description: 'Azure resource & cluster names')

    }

    stages {
        // check if this is the very first run, if it is then
        // abort the pipeline as it will know nothing about parameters
        stage("initial run") {
            when {
                expression {
                    params.input_name == ''
                }
            }
            steps {
                echo 'INITIAL RUN COMPLETED. JOB PARAMETERIZED.'
            }
        }

/*
                script { 
                    if ("${params.input_name}" == '') { 
                        currentBuild.result = 'ABORTED' 
                        error('DRY RUN COMPLETED. JOB PARAMETERIZED.') 
                    } 
                } 
            } 
        } 
*/
    
        // clean out the existing workspace
        stage ('clean workspace') {
            steps {
                step([$class: 'WsCleanup'])
            }
        }

        // perform a checkout of the repo specified in the Jenkins job
        stage('checkout') {
            steps {
                 checkout scm
            }
        }

        // display some variables
        stage ('debug vars') {
            steps {
                sh '''
                    env > ${WORKSPACE}/files/env.txt
                '''
            }
        }

         // create Azure RG & AKS cluster
        stage('create cluster')  {
            when {
                expression {
                    params.input_name != ''
                }
            }
            steps {
                wrap([$class: 'AnsiColorBuildWrapper', colorMapName: "xterm"]) {
                    sh 'ansible-playbook ${WORKSPACE}/playbooks/aks-cluster.yml \
                                         --extra-vars "input_name=${input_name}" \
                                    || echo "Errors encountered during playbook execution"'
                }
            }
        }

        // clean out the current workspace
/*        stage ('clean workspace') {
            steps {
                step([$class: 'WsCleanup'])
            }
        }
*/
    }

    post { 
        always { 
            // office365ConnectorSend message: "Build finished from: ${env.BRANCH_NAME} : ${currentBuild.durationString}", status:"${currentBuild.currentResult}", webhookUrl: "https://outlook.office.com/webhook/646fca5d-346e-49d7-bed7-a44e224f9778@e0793d39-0939-496d-b129-198edd916feb/IncomingWebhook/8fda581e027242f28354bd3721a42b3e/149b01b3-7a9d-41a8-9513-ceb0af2a4eae"
            echo "Build finished from: ${env.BRANCH_NAME} (${currentBuild.durationString}), status: ${currentBuild.currentResult}, webhookUrl: https://outlook.office.com/webhook/646fca5d-346e-49d7-bed7-a44e224f9778@e0793d39-0939-496d-b129-198edd916feb/IncomingWebhook/8fda581e027242f28354bd3721a42b3e/149b01b3-7a9d-41a8-9513-ceb0af2a4eae"
        }
    }
}
