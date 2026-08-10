pipeline {
    agent any

    environment {
        REACT_APP_VERSION = "1.2.${env.BUILD_ID}"
        AWS_DEFAULT_REGION = 'us-east-1'
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
                    args "--entrypoint=''"
                }
            }

 /*           environment{
                
            }
*/
            steps{
                withCredentials([usernamePassword(credentialsId: 'my-aws', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                    sh'''
                        aws --version 
                        aws ecs register-task-definition --cli-input-json file://aws/task-definition-prod.json
                        aws ecs update-service --cluster LearnJenkinsApp-Cluster-Prod-Dejan  --service LearnJenkinsApp-Service-Prod-Dejan  --task-definition LearnJenkinsApp-TaskDefinition-Prod-Dejan:2
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
