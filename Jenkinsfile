pipeline {
    agent any

    environment {
        PYTHON = 'C:\\Users\\raviv\\AppData\\Local\\Programs\\Python\\Python314\\python.exe'
        MONGO_URI = 'mongodb://localhost:27017/test_student_db'
        SECRET_KEY = 'jenkins-staging-secret'
        STAGING_DIR = 'C:\\Jenkins-Staging\\flask_Practice'
    }

    stages {

        stage('Build') {
            steps {
                echo 'Installing Python dependencies...'
                bat '"%PYTHON%" -m pip install -r requirements.txt'
            }
        }

        stage('Test') {
            steps {
                echo 'Running pytest...'
                bat '"%PYTHON%" -m pytest -v'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying Flask application to staging...'

                bat '''
                    if not exist "%STAGING_DIR%" mkdir "%STAGING_DIR%"
                    robocopy . "%STAGING_DIR%" /E /XD .git screenshots
                    if %ERRORLEVEL% LEQ 7 exit /B 0
                    exit /B %ERRORLEVEL%
                '''

                echo 'Staging deployment completed successfully.'
                echo 'Application files deployed to: %STAGING_DIR%'
            }
        }
    }

    post {
    success {
        echo 'Jenkins CI/CD pipeline completed successfully.'

        emailext(
            subject: "SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
            body: """Jenkins pipeline completed successfully.

Job: ${env.JOB_NAME}
Build: #${env.BUILD_NUMBER}
Status: ${currentBuild.currentResult}

Build URL: ${env.BUILD_URL}
""",
            to: "raviveera2305@gmail.com"
        )
    }

    failure {
        echo 'Jenkins CI/CD pipeline failed.'

        emailext(
            subject: "FAILURE: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
            body: """Jenkins pipeline failed.

Job: ${env.JOB_NAME}
Build: #${env.BUILD_NUMBER}
Status: ${currentBuild.currentResult}

Build URL: ${env.BUILD_URL}
""",
            to: "raviveera2305@gmail.com"
        )
    }
}
}