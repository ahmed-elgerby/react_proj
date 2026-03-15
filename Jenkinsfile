pipeline{
    agent{
        label 'docker'
    }
    environment {
        // Set your AWS credentials stored in Jenkins credentials
        AWS_ACCESS_KEY = credentials('secret.aws-access-key-id')   // Jenkins credential ID
        AWS_SECRET_KEY = credentials('secret.aws-secret-access-key') // Jenkins credential ID
        APP_NAME = 'myapp'
        ENV_NAME = 'myenv'
        REGION = 'us-east-1'
        DEPLOY_ZIP = 'deploy.zip'
    }
    stages{
        stage('Build Docker Image'){
            steps{
                echo 'Building the application...'
                sh 'docker build -t ahmedelgerby/react-app -f Dockerfile .'
            }
        }
        stage('Test'){
            steps{
                echo 'Running tests...'
                sh 'docker run -e CI=true ahmedelgerby/react-app npm run test'
            }
        }
        stage('ZIP Application for Deployment'){
            steps{
                echo 'Zipping application for deployment...'
                sh 'zip -r ${DEPLOY_ZIP} * -x node_modules/* -x .git/*'
            }
        }
        stage('Deploy to AWS'){
            steps{
                echo 'Deploying to AWS...'
                withEnv(["AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY}", "AWS_SECRET_ACCESS_KEY=${AWS_SECRET_KEY}"]) {
                    sh """
                        aws elasticbeanstalk create-application-version \
                            --application-name ${APP_NAME} \
                            --version-label build-${env.BUILD_NUMBER} \
                            --source-bundle S3Bucket=mybucket,S3Key=${DEPLOY_ZIP} \
                            --region ${REGION}
                        
                        aws elasticbeanstalk update-environment \
                            --environment-name ${ENV_NAME} \
                            --version-label build-${env.BUILD_NUMBER} \
                            --region ${REGION}
                    """}
            }
        }
    }
}

//i am adding this line to test number 7