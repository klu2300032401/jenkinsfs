pipeline {
    agent any

    stages {

        // ===== FRONTEND BUILD =====
        stage('Build Frontend') {
            steps {
                dir('reactfrontend') { // frontend folder containing package.json
                    bat 'npm install'
                    bat 'npm run build'
                }
            }
        }

        // ===== FRONTEND DEPLOY =====
        stage('Deploy Frontend to Tomcat') {
            steps {
                bat """
                if exist "C:\\Program Files\\Apache Software Foundation\\Tomcat 10.1\\webapps\\jenkinsreact" (
                    rmdir /S /Q "C:\\Program Files\\Apache Software Foundation\\Tomcat 10.1\\webapps\\jenkinsreact"
                )
                mkdir "C:\\Program Files\\Apache Software Foundation\\Tomcat 10.1\\webapps\\jenkinsreact"
                xcopy /E /I /Y reactfrontend\\dist\\* "C:\\Program Files\\Apache Software Foundation\\Tomcat 10.1\\webapps\\jenkinsreact"
                """
            }
        }

        // ===== BACKEND BUILD =====
        stage('Build Backend') {
            steps {
                script {
                    // Automatically find the folder containing pom.xml
                    def backendDir = ''
                    new File(pwd()).eachDir { dir ->
                        if (new File(dir, 'pom.xml').exists()) {
                            backendDir = dir.name
                        }
                    }
                    if (backendDir) {
                        echo "Found backend folder: ${backendDir}"
                        dir(backendDir) {
                            bat 'mvn clean package'
                        }
                    } else {
                        error "No backend folder with pom.xml found!"
                    }
                }
            }
        }

        // ===== BACKEND DEPLOY =====
        stage('Deploy Backend to Tomcat') {
            steps {
                script {
                    def backendDir = ''
                    new File(pwd()).eachDir { dir ->
                        if (new File(dir, 'pom.xml').exists()) {
                            backendDir = dir.name
                        }
                    }
                    if (backendDir) {
                        bat """
                        if exist "C:\\Program Files\\Apache Software Foundation\\Tomcat 10.1\\webapps\\jenkinsspringboot.war" (
                            del /Q "C:\\Program Files\\Apache Software Foundation\\Tomcat 10.1\\webapps\\jenkinsspringboot.war"
                        )
                        if exist "C:\\Program Files\\Apache Software Foundation\\Tomcat 10.1\\webapps\\jenkinsspringboot" (
                            rmdir /S /Q "C:\\Program Files\\Apache Software Foundation\\Tomcat 10.1\\webapps\\jenkinsspringboot"
                        )
                        copy "${backendDir}\\target\\*.war" "C:\\Program Files\\Apache Software Foundation\\Tomcat 10.1\\webapps\\jenkinsspringboot.war"
                        """
                    } else {
                        error "No backend folder with pom.xml found for deployment!"
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment Successful!'
        }
        failure {
            echo 'Pipeline Failed.'
        }
    }
}
