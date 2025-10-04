pipeline {
    agent any
    tools {
        maven 'maven-3.9.11'
    }

    stages {
        stage('SCM') {
            steps {
                git branch: "${env.BRANCH_NAME}", credentialsId: 'git-cred', url: 'https://github.com/VijayDesai08/maven-project.git'
            }
        }
        
        stage('BUILD WAR FILE') {
            steps {
                sh 'mvn clean install'
            }
        }    
        
        stage('BUILD IMAGE') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred') {
                        sh 'docker build -t vijay008/maven-project-${env.BRANCH_NAME}:v$BUILD_NUMBER .'
                    }
                }
            }
        }

        
        stage('PUSH IMAGE') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred') {
                        sh 'docker push vijay008/maven-project-${env.BRANCH_NAME}:v$BUILD_NUMBER'
                    }
                }
            }
        }
        
        stage('DEPLOY IMAGE') {
            steps {
                sh 'docker stop demo-cont || true'
                sh 'docker run --rm --name demo-cont -d -p 8585:8080 vijay008/maven-project-${env.BRANCH_NAME}:v$BUILD_NUMBER'
            }
        }
    }
}
