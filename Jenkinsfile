pipeline {
    agent any

    environment {
        REACT_APP_VERSION = "1.0.$BUILD_ID"
        AWS_DEFAULT_REGION = "us-east-1"
    }
    
    stages {
        stage('Deploy to AWS') {
            agent {
                docker {
                    image 'amazon/aws-cli'
                    reuseNode true
                    args  "-u root --entrypoint=''"
                }
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'my-aws', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                    sh '''
                        aws --version
                        yum install jq -y
                        LATEST_TD_REVISION=$(aws ecs register-task-definition --cli-input-json file://aws/learnjenkins-taskdefinition-prod.json --memory=512 | jq ".taskDefinition.revision")
                        echo $LATEST_TD_REVISION
                        aws ecs update-service --cluster learnjenkins-cluster --service learnjenkins-taskdefinition-prod-service-aevg29sc --task-definition learnjenkins-taskdefinition-prod:$LATEST_TD_REVISION
                        aws ecs wait sevices-stable --cluster learnjenkins-cluster --services learnjenkins-taskdefinition-prod-service-aevg29sc
                    '''
                }
            }
        }
        stage('build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo 'Building...'
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -la
                '''
            }
        }
    }
}
