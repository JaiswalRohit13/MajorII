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
        stage('Image build') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker', toolName: 'docker') {
                        sh '''
                        docker build -t emp_portal-app .
                        '''
                    }
            }
        } 
    }
     stage('Docker tag') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker', toolName: 'docker') {
                        sh '''
                        docker tag emp_portal-app jaiswalrohit13/emp_portal-app:latest
                        '''
                    }
            }
        } 
    } 
      stage('Docker run') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker', toolName: 'docker') {
                        sh '''
                        docker run -d -p 5000:5000 emp_portal-app:latest
                        '''
                    }
            }
        } 
    }  
    stage('Docker push') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker', toolName: 'docker') {
                        sh '''
                        docker push jaiswalrohit13/emp_portal-app:latest
                        '''
                    }
            }
        } 
    }
    }
}
