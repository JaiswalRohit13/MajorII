pipeline {
    agent any
    
    environment {
        SCANNER_HOME=tool 'sonar-scanner'
      }
    stages {
        stage('Checkout') {
            steps {
               git branch: 'main', url: 'https://github.com/JaiswalRohit13/MajorII.git'
            }
        }
        stage('Installing packages') {
            steps {
                script {
                    // Create a virtual environment
                    sh 'python3 -m venv venv'
                    
                    // Install required python packages within the virtual environment
                    sh './venv/bin/pip install -r requirements.txt'
                }
            }
        }
    }
}
