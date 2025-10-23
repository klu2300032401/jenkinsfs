pipeline {
    agent any

    environment {
        TOMCAT_PATH = 'C:\\Program Files\\Apache Software Foundation\\Tomcat 10.1\\webapps'
        FRONTEND_NAME = 'jenkinsfrontendapi'   // Must match base in vite.config.js
        BACKEND_WAR = 'jenkinsfullstack.war'
    }

    stages {

        // ===== CHECKOUT CODE =====
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        // ===== FRONTEND BUILD =====
        stage('Build Frontend') {
            steps {
                dir('reactfrontend') {
                    bat 'npm install'
                    bat 'npm run build'
                }
            }
        }

        // ===== FRONTEND DEPLOY =====
        stage('Deploy Frontend to Tomcat') {
            steps {
                bat """
                if exist "${TOMCAT_PATH}\\${FRONTEND_NAME}" rmdir /S /Q "${TOMCAT_PATH}\\${FRONTEND_NAME}"
                mkdir "${TOMCAT_PATH}\\${FRONTEND_NAME}"
                xcopy /E /I /Y reactfrontend\\dist\\* "${TOMCAT_PATH}\\${FRONTEND_NAME}"
                """
            }
        }

        // ===== BACKEND BUILD =====
        stage('Build Backend') {
            steps {
                dir('springbootbackend') {
                    bat 'mvn clean package'
                }
            }
        }

        // ===== BACKEND DEPLOY =====
        stage('Deploy Backend to Tomcat') {
            steps {
                bat """
                copy springbootbackend\\target\\${BACKEND_WAR} "${TOMCAT_PATH}"
                """
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the logs for details.'
        }
    }
}
