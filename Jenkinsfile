pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh "./gradlew build"

            }
        }

        stage('Deploy to Develop') {
            when {
                branch 'develop'
            }
            steps {
                sh "\$(/home/tomcat/.local/bin/aws ecr get-login --no-include-email --region us-east-1)"
                script {
                    docker.build("release-plugin-jenkins-pipeline:develop")
                }
                sh "./gradlew artifactoryPublish"
            }
        }

        stage('Deploy to Staging') {
            when {
                expression { BRANCH_NAME ==~ /release\/[0-9]+\.[0-9]+\.[0-9]+/ }
            }
            steps {
                sh "./gradlew release -Prelease.useAutomaticVersion=true"
                sh "\$(/home/tomcat/.local/bin/aws ecr get-login --no-include-email --region us-east-1)"
                script {
                    docker.build("release-plugin-jenkins-pipeline:staging")
                }
            }
        }

        stage('Deploy to Prod') {
            when {
                branch 'master'
            }
            steps {
                sh "\$(/home/tomcat/.local/bin/aws ecr get-login --no-include-email --region us-east-1)"
                script {
                    docker.build("release-plugin-jenkins-pipeline:latest")
                }
            }
        }

    }
}