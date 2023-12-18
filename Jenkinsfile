pipeline {
    agent any
    
    environment {
        TOMCAT_CONTAINER_NAME = 'tomcat-tomcat-1'
        REMOTE_SERVER = '34.205.65.197'
        REMOTE_USER = 'ubuntu'
        PRIVATE_KEY_FILE = '/home/ahmed/AS/ansible.pem'
        GIT_REPO_URL = 'https://github.com/AhmedS0liman/hello-world.git'
        PROJECT_NAME = 'sparkjava-hello-world-1.0'
    }
    
    stages {
        stage('Checkout') {
            steps {
                // Checkout your Git repository
                git url: GIT_REPO_URL
            }
        }
        
        stage('Build') {
            steps {
                // Build the Maven project
                sh 'mvn clean package'
            }
        }
        
        stage('Copy to Remote Server') {
            steps {
                // Copy the generated WAR file to the Tomcat webapps folder inside the Docker container on the remote server
                script {
                    CONTAINER_PATH = sh(script: "ssh -i ${PRIVATE_KEY_FILE} ${REMOTE_USER}@${REMOTE_SERVER} 'docker exec -i ${TOMCAT_CONTAINER_NAME} /bin/bash -c \"cd /usr/local/tomcat/webapps && pwd\"'", returnStdout: true).trim()
                    sh "docker cp target/${PROJECT_NAME}.war ${REMOTE_SERVER}:${CONTAINER_PATH}/"
                }
            }
        }
        
        stage('Restart Tomcat Container') {
            steps {
                // Restart Tomcat Docker container on the remote server
                sh "ssh -i ${PRIVATE_KEY_FILE} ${REMOTE_USER}@${REMOTE_SERVER} 'docker restart ${TOMCAT_CONTAINER_NAME}'"
            }
        }
        
        stage('Wait for Deployment') {
            steps {
                // Wait for Tomcat deployment (you may need to adjust the sleep duration)
                sleep time: 60, unit: 'SECONDS'
            }
        }
    }
    
    post {
        always {
            // Stop Tomcat Docker container on the remote server after the tests (you may want to adjust this based on your needs)
            script {
                sh "ssh -i ${PRIVATE_KEY_FILE} ${REMOTE_USER}@${REMOTE_SERVER} 'docker stop ${TOMCAT_CONTAINER_NAME}'"
            }
        }
    }
}

