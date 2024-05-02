pipeline {
    agent any
    
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
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

        stage('Clean Up') {
            steps {
                script {
                    // Stop and remove all containers except for SonarQube, Python, and the specified container ID
                    sh '''
                    docker ps -a --format "{{.ID}} {{.Names}}" | grep -v "08edb5bed32e\\|sonarqube:lts-community\\|python" | awk '{print $1}' | xargs -r docker stop
                    docker ps -a --format "{{.ID}} {{.Names}}" | grep -v "08edb5bed32e\\|sonarqube:lts-community\\|python" | awk '{print $1}' | xargs -r docker rm
                    docker images --format "{{.Repository}}:{{.Tag}}" | grep -v "sonarqube:lts-community\\|python" | xargs -r docker rmi -f
                    '''
                }
            }
        }
        stage("Sonarqube Analysis "){
            steps{
                withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=emp-portal \
                    -Dsonar.projectKey=emp-portal '''
                }
            }
        }
        stage('Quality Gate') {
            steps {
                script{
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
            }
        }
       }
        stage('TRIVY FS SCAN') {
            steps {
                sh "trivy fs . > trivyfs.txt"
            }
        }
        stage('OWASP FS SCAN') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'dp-check'
                dependencyCheckPublisher pattern: '/dependency-check-report.xml'
            }
        }
    }
}
