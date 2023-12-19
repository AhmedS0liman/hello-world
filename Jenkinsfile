pipeline {
    agent any

    environment {
        TOMCAT_HOST = '34.205.65.197'
        TOMCAT_PORT = '8080'
        TOMCAT_USER = 'war-deployer'
        TOMCAT_PASSWORD = 'pass123'
        TOMCAT_CONTEXT_PATH = 'sparkjava-hello-world-1.0'
        TOMCAT_MANAGER_URL = "http://${TOMCAT_USER}:${TOMCAT_PASSWORD}@${TOMCAT_HOST}:${TOMCAT_PORT}/manager/text"
        DOCKER_TOMCAT_CONTAINER_ID = 'c966767a03cf'
        DOCKER_JENKINS_CONTAINER_ID = '97f6923761a8'
        GITHUB_REPO_URL = 'https://github.com/AhmedS0liman/hello-world.git'
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    // Assuming Git is installed in your Jenkins container
                    git branch: 'main', url: GITHUB_REPO_URL
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    // Build the Maven project
                    docker.exec(DOCKER_JENKINS_CONTAINER_ID, "docker run --rm -v $(pwd):/app -w /app maven:3.8-jdk-11 mvn clean install")
                }
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                script {
                    def warFileName = sh(script: 'ls target/*.war', returnStdout: true).trim()

                    // Deploy the WAR file to Tomcat
                    sh "curl -v -u ${TOMCAT_USER}:${TOMCAT_PASSWORD} --upload-file target/${warFileName} ${TOMCAT_MANAGER_URL}/deploy?path=/${TOMCAT_CONTEXT_PATH}"
                }
            }
        }
    }
}
