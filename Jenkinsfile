pipeline {
    agent any

    environment {
        APP_NAME   = 'React_Project'
        ENV_NAME   = 'ReactProject-env'
        REGION     = 'us-east-1'
        DEPLOY_ZIP = 'deploy.zip'
    }

    stages {
        stage('Build Docker Image') {
            steps {
                echo 'Building the application...'
                sh 'docker build -t ahmedelgerby/react-app -f Dockerfile .'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'docker build -t ahmedelgerby/react-app-dev -f Dockerfile.dev .'                
                sh 'docker run --rm -e CI=true ahmedelgerby/react-app-dev npm run test -- --watchAll=false'
            }
        }


        stage('ZIP Application for Deployment') {
            steps {
                echo 'Zipping application for deployment...'
                sh 'zip -r ${DEPLOY_ZIP} * -x node_modules/* -x .git/*'
            }
        }

        stage('Deploy to AWS') {
            steps {
                echo 'Deploying to AWS...'
                withAWS(credentials: 'AWS Creds Now', region: "${REGION}") {
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
                    """
                }

            }
        }
    }
}

//25
