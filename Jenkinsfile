pipeline {
    agent any

    environment {
        AWS_ID = credentials("AWS-ACCOUNT-ID")
        REGION = 'us-east-1'
        PROJECT = 'bank-microservice'
        COMMIT_HASH = '${sh(script: "git rev-parse --short HEAD", returnStdout: true).trim()}'
    }

    stages {
        stage("test") {
            steps {
                sh 'mvn clean test'
            }
        }

        stage("package")  {
            steps {
                sh 'mvn package -DskipTests'
            }
        }

        stage("Docker Build") {
            steps {
                sh 'aws ecr get-login-password --region ${REGION} | docker login --username AWS --password-stdin ${AWS_ID}.dkr.ecr.${REGION}.amazonaws.com'
                sh 'docker build -t ${PROJECT}-kwi:${COMMIT_HASH} .'
                sh 'docker tag ${PROJECT}-kwi:${COMMIT_HASH} ${AWS_ID}.dkr.ecr.${REGION}.amazonaws.com/${PROJECT}-kwi:${COMMIT_HASH}'
                sh 'docker push ${AWS_ID}.dkr.ecr.${REGION}.amazonaws.com/${PROJECT}-kwi:${COMMIT_HASH}'
            }
        }
    }
}