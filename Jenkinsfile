pipeline {
    agent {
      label 'AGENT-1'
    }
    options {
        timeout(time: 300, unit: 'SECONDS')
        disableConcurrentBuilds()
    }
    parameters{
        choice(name: 'ENVIRONMENT', choices: ['dev', 'qa', 'uat', 'pre-prod', 'prod'], description: 'Select your Environment')
        string(name: 'version',  description: 'Enter your application version')
        //string(name: 'jira-id',  description: 'Enter your jira id')
    }

    environment {
        appVersion = ''
        account_id = '596059882666'
        region = 'us-east-1'
        project = 'expense'
        environment = 'development'
        component = 'backend'
    }
    stages {
        stage('Setup Environment'){
            steps{
                script{
                    environment = params.ENVIRONMENT
                    appVersion = params.version
                    //account_id = pipelineGlobals.getAccountID(environment)
                }
            }
        }
        stage('Deploy') {
            steps {
                withAWS(region: 'us-east-1', credentials: 'aws-creds') {
                    sh """
                        aws eks update-kubeconfig --region ${region} --name ${project}-development
                        cd helm
                        sed -i 's/IMAGE_VERSION/${appVersion}/g' values-dev.yaml
                        helm upgrade --install ${component} -n ${project} -f values-dev.yaml .
                    """
                }
            }
        }
    }

    post {
        always{
            echo "This sections runs always"
            deleteDir()
        }
        success {
            echo "Build Successful"
        }
        failure {
            echo "Build Failed"
        }
        unstable {
            echo "Build Unstable"
        }
        changed {
            echo "Status changed from previous build"
        }
    }
}
