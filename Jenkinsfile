pipeline {
    agent any

    tools {
        nodejs 'Node-18' // ✅ This must match your Jenkins NodeJS config
    }

    environment {
        AWS_REGION = 'us-east-1'            // ✅ Replace with your region
        S3_BUCKET = 'your-s3-bucket-name'    // ✅ Replace with your bucket name
        BUILD_DIR = 'build'                  // ✅ Default for React
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install --legacy-peer-deps'

            }
        }

        stage('Build Application') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Upload to S3') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'aws-creds',               // ✅ Jenkins Credential ID
                        usernameVariable: 'AWS_ACCESS_KEY_ID',
                        passwordVariable: 'AWS_SECRET_ACCESS_KEY'
                    )
                ]) {
                    sh '''
                        aws configure set aws_access_key_id $AWS_ACCESS_KEY_ID
                        aws configure set aws_secret_access_key $AWS_SECRET_ACCESS_KEY
                        aws configure set region $AWS_REGION

                        echo "Uploading to S3..."
                        aws s3 cp $BUILD_DIR s3://$S3_BUCKET/ --recursive
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '✅ React app deployed to S3!'
        }
        failure {
            echo '❌ Deployment failed.'
        }
    }
}
