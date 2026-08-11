pipeline {
    agent any

    environment {
        REACT_APP_VERSION = "1.2.${env.BUILD_ID}"
        AWS_DEFAULT_REGION = 'us-east-1'
        AWS_ECS_CLUSTER = 'LearnJenkinsApp-Cluster-Prod-Dejan'
        AWS_ECS_SERVICE_PROD = 'LearnJenkinsApp-Service-Prod-Dejan'
        AWS_ECS_TD_PROD = 'LearnJenkinsApp-TaskDefinition-Prod-Dejan'

    }

    stages {

        stage('Build Docker Image') {
            steps{
                sh 'docker build -t my-playwright .'
            }
         }

        stage('Deploy to AWS'){
            agent{
                docker{
                    image 'amazon/aws-cli:2.36.17'
                    reuseNode true
                    args "-u root --entrypoint=''"
                }
            }

 /*           environment{
                
            }
*/
            steps{
                withCredentials([usernamePassword(credentialsId: 'my-aws', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                    sh'''
                        aws --version 
                        yum install jq -y
                        LATEST_TD_REVISION=$(aws ecs register-task-definition --cli-input-json file://aws/task-definition-prod.json | jq '.taskDefinition.revision')
                        aws ecs update-service --cluster $AWS_ECS_CLUSTER --service $AWS_ECS_SERVICE_PROD --task-definition $AWS_ECS_TD_PROD:$LATEST_TD_REVISION
                        aws ecs wait services-stable --cluster $AWS_ECS_CLUSTER  --services $AWS_ECS_SERVICE_PROD
                    '''
                }
            }
        }

        stage('Build') {
            agent {
                docker {
                    image 'node:22-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
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
