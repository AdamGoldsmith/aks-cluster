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
                script {
                    currentBuild.result = 'SUCCESS'
                }
                error('INITIAL RUN COMPLETED, JOB PARAMETERIZED')
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
/*        stage('create cluster')  {
            when {
                expression {
                    params.input_name != null
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
*/

        // clean out the current workspace
/*        stage ('clean workspace') {
            steps {
                step([$class: 'WsCleanup'])
            }
        }
*/
    }
}
