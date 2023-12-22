pipeline {
    agent any
    
    tools{
        jdk 'jdk17'
        nodejs 'node16'
        
    }
    
    environment{
        SCANNER_HOME= tool 'sonar-scanner'
    }
    
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/harshitasaxena05/fullstack-bank.git'
            }
        }
        
        stage('OWASP FS SCAN') {
            steps {
                dependencyCheck additionalArguments: '--scan ./app/backend --disableYarnAudit --disableNodeAudit', odcInstallation: 'DC'
                    dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
        stage('TRIVY FS SCAN') {
            steps {
                sh "trivy fs ."
            }
        }
        
        stage('SONARQUBE ANALYSIS') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh " $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Bank -Dsonar.projectKey=Bank "
                }
            }
        }
        
        
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }

        stage('Directorychk Stage') {
            steps {
                sh '''
                    # Print the current working directory
                    currentDir=$(pwd)
                    echo "Current directory: ${currentDir}"
                '''
            }
        }
        
        stage('Backend') {
            steps {
                dir('/var/lib/jenkins/workspace/bank/app/backend') {
                    sh "npm install"
                }
            }
        }
        
        stage('frontend') {
            steps {
                dir('/var/lib/jenkins/workspace/bank/app/frontend') {
                    sh "npm install"
                }
            }
        }
        
        stage('Deploy to Conatiner') {
            steps {
                 dir('/var/lib/jenkins/workspace/bank/app/') {
                    sh "docker-compose up -d"
                }
            }
        }
    }
}
