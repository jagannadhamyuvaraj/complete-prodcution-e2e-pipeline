pipeline{
    agent{
        label "slave"
    }
    tools {
        maven 'Maven3'
        // sonarqube 'sonarqube-scanner'
    }
    environment {
        APP_NAME = "complete-production-e2e-pipeline"
        RELEASE = "1.0.0"
        DOCKER_USER = "jagannadhamyuvaraj"
        DOCKER_PASS = "dockerhub"
        IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
    }
    stages{
        stage("Cleanup Workspace"){
            steps {
                cleanWs()
            }

        }
    
        stage("Checkout from SCM"){
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/jagannadhamyuvaraj/complete-prodcution-e2e-pipeline.git'
            }

        }

        stage("Build Application"){
            steps {
                sh "mvn clean package"
            }

        }

        stage("Test Application"){
            steps {
                sh "mvn test"
            }
        }
        stage("Code Analysis"){
            steps{
                script{
                withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-token'){
                  sh "mvn sonar:sonar"
                }
                }
            }
        }
        
        stage("Build & Push Docker Image"){
            steps {
                script {
                    docker.withRegistry('',DOCKER_PASS) {
                        docker_image = docker.build "${IMAGE_NAME}"
                    }
                    docker.withRegistry('',DOCKER_PASS) {
                        docker_image.push("${IMAGE_TAG}")
                        docker_image.push('latest')
                    }
                }
            }
        }
    } //stages closing
} //pipeline closing
