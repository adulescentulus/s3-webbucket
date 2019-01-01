pipeline {
    agent any
    options {
        buildDiscarder(logRotator(numToKeepStr: '30', artifactNumToKeepStr: '15'))
    }
    stages {
        stage ('Initialize') {
            steps {
                sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                    '''
            }
        }
        stage('Deploy') {
            agent {
                docker { 
                    image 'grolland/aws-cli'
                    reuseNode true 
                }
            }
            environment {
                AWS_DEFAULT_REGION = 'eu-central-1'
                AWS_ACCESS_KEY_ID = credentials('AWS_KEY_IMGRESIZE_ID')
                AWS_SECRET_ACCESS_KEY = credentials('AWS_KEY_IMGRESIZE_KEY')
                STACK_NAME = 'BNC-staging'
                WEBBUCKET_NAME = env.BNC_HUGO_STAGING_NAME
            }
            steps {
                sh '''
                    bin/deploy 
                    '''
                s3Upload consoleLogLevel: 'INFO', dontWaitForConcurrentBuildCompletion: false, entries: [[bucket: 'nc-infra-cfn-jenkins-artifacts', excludedFile: '', flatten: false, gzipFiles: false, keepForever: false, managedArtifacts: true, noUploadOnFailure: true, selectedRegion: 'eu-central-1', showDirectlyInBrowser: false, sourceFile: 'deploy/*.yaml', storageClass: 'STANDARD', uploadFromSlave: false, useServerSideEncryption: false]], pluginFailureResultConstraint: 'FAILURE', profileName: 'ARTIFACTS', userMetadata: []
            }
        }
    }
    post { 
        always { 
            cleanWs()
        }
    }
}