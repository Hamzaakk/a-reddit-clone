pipeline {
    agent any
    tools {
        jdk 'jdk17'  // Replace with your actual JDK if needed
        nodejs 'node16'
    }
    environment {
        // JAVA_HOME = '/usr/lib/jvm/java-11-openjdk-amd64'
        // PATH = "${JAVA_HOME}/bin:${PATH}"
        SCANNER_HOME = tool 'sonar-scanner'
        APP_NAME = "reddit-clone-pipeline"
        RELEASE = "1.0.0"
        DOCKER_USER = "hamzaakkaoui"
        DOCKER_PASS = 'dockerhub'
        IMAGE_NAME = "${DOCKER_USER}/${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
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
                    sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Redit-Clone-CI \
                    -Dsonar.projectKey=Redit-Clone-CI'''
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
        /*
        stage('Build & Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('', DOCKER_PASS) {
                        docker_image = docker.build "${IMAGE_NAME}"
                    }
                    docker.withRegistry('', DOCKER_PASS) {
                        docker_image.push("${IMAGE_TAG}")
                        docker_image.push('latest')
                    }
                }
            }
        }
        */
    }
    // post {
    //     always {
    //         emailext attachLog: true,
    //             subject: "'${currentBuild.result}'",
    //             body: "Project: ${env.JOB_NAME}<br/>" +
    //                 "Build Number: ${env.BUILD_NUMBER}<br/>" +
    //                 "URL: ${env.BUILD_URL}<br/>",
    //             to: 'your-email@example.com',
    //             attachmentsPattern: 'trivyfs.txt,trivyimage.txt'
    //     }
    // }
}
