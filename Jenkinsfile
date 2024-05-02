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
                    sh 'python3 -m venv venv'
                    sh 'source venv/bin/activate'
                    sh 'pip install -r requirements.txt'               
                }
            }
        }
    }
}
