pipeline {
    agent {
        docker {
            image 'openjdk:17' // Use an appropriate Docker image
            args '-v /var/jenkins/agents:/var/jenkins/agents'
        }
    }
    tools {
        jdk 'jdk17'
        nodejs 'node16' 
    }
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
        APP_NAME = "reddit-clone-pipeline"
        RELEASE = "1.0.0"
        DOCKER_USER = "hamzaakkaoui"
        DOCKER_PASS = 'dockerhub'
        IMAGE_NAME = "${DOCKER_USER}/${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
        JAVA_HOME = '/usr/lib/jvm/java-11-openjdk-amd64'
        PATH = "${JAVA_HOME}/bin:${PATH}"
    }
    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Checkout from Git') {
            steps {
                git branch: 'main', url: 'https://github.com/Hamzaakk/a-reddit-clone'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                 withSonarQubeEnv('SonarQube-Server') {
                   sh """
                    sonar-scanner \
                      -Dsonar.projectKey=Redit-Clone-CI \
                      -Dsonar.sources=. \
                      -Dsonar.host.url=http://54.221.2.11:9000 \
                      -Dsonar.token=sqp_dff50b61668588a4888770c37bac8535424ad12c
                    """
                }
            }
        }
        stage('Quality Gate') {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'SonarQube-Token'
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }
        // Optional Docker stages
        stage('Build & Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('', DOCKER_PASS) {
                        docker_image = docker.build "${IMAGE_NAME}:${IMAGE_TAG}"
                    }
                    docker.withRegistry('', DOCKER_PASS) {
                        docker_image.push("${IMAGE_TAG}")
                        docker_image.push('latest')
                    }
                }
            }
        }
    }
}
