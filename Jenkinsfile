#!/usr/bin/env groovy

pipeline {
    agent any

    tools {
        maven 'Maven'
    }

    environment {
        //need to adjust with actual variable value
        ECR_REPO_URL = '1234567891.dkr.ecr.us-east-1.amazonaws.com'
        ECR_APP_NAME = 'jenkins-aws-java-maven-app'
        SERVER_INSTANCE_IP = '000.000.00.00'
        SERVER_INSTANCE_USER = 'ubuntu'
        GIT_REPO_URL = 'github.com/user/repo-name.git'
        IMAGE_REPO = "$ECR_REPO_URL/$ECR_APP_NAME"
    }

    stages {
        stage('increment version') {
            steps {
                script {
                    echo 'incrementing app version...'
                    sh 'mvn build-helper:parse-version versions:set \
                        -DnewVersion=\\\${parsedVersion.majorVersion}.\\\${parsedVersion.minorVersion}.\\\${parsedVersion.nextIncrementalVersion} \
                        versions:commit'
                    def matcher = readFile('pom.xml') =~ '<version>(.+)</version>'
                    def version = matcher[0][1]
                    env.IMAGE_NAME = "$version-$BUILD_NUMBER"
                    echo "############ ${IMAGE_REPO}"
                }
            }
        }

        stage('build app') {
            steps {
                script {
                    echo 'building the application...'
                    sh 'mvn clean package'
             steps {
                   sh 'pwd'
                   sh 'ls -l'
                   }
                }
            }
        }
       stage('SonarQube Analysis') {
         steps {
            script {
                echo 'Running SonarQube analysis...'
                withSonarQubeEnv('sq1') { 
                    sh '''
                        mvn sonar:sonar \
                            -Dsonar.projectKey=jenkins-aws-java-maven-app \
                            -Dsonar.projectName="Jenkins AWS Java Maven App" \
                            -Dsonar.projectVersion=${BUILD_NUMBER}
                    '''
                }
            }
        }
    }
    
        
    }
}
