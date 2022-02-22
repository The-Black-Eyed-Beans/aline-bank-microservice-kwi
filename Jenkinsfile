pipeline {
    agent any

    tools {
        maven 'Maven'
    }

    environment {
        AWS_ID = credentials("AWS-ACCOUNT-ID")
        REGION = 'us-east-1'
        PROJECT = 'bank-microservice'
        COMMIT_HASH = '${sh(script: "git rev-parse --short HEAD", returnStdout: true).trim()}'
    }

    stages {
        stage("Test") {
            steps {
                sh 'git submodule init'
                sh 'git submodule update'
                sh 'mvn clean test'
            }
        }

        stage("Package")  {
            steps {
                sh 'mvn package -DskipTests'
            }
        }

        stage("Docker Build") {
            steps {
                sh 'aws ecr get-login-password --region ${REGION} --profile keshaun | sudo docker login --username AWS --password-stdin ${AWS_ID}.dkr.ecr.${REGION}.amazonaws.com'
                sh 'docker build -t ${PROJECT}-kwi:${COMMIT_HASH} .'
                sh 'docker tag ${PROJECT}-kwi:${COMMIT_HASH} ${AWS_ID}.dkr.ecr.${REGION}.amazonaws.com/${PROJECT}-kwi:${COMMIT_HASH}'
                sh 'docker push ${AWS_ID}.dkr.ecr.${REGION}.amazonaws.com/${PROJECT}-kwi:${COMMIT_HASH}'
            }
        }
    }
}