pipeline{
    agent {label 'Jenkins-Agent'}
    tools {
        jdk 'Java17'
        maven 'Maven3'
    }
    stages{
        stage('Cleanup Workspace'){
            steps{
                echo 'Cleaning up the workspace...'
                cleanWs()
            }
        }
        stage('Checkout from SCM'){
            steps{
                echo 'Checkingout from SCM...'
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/Ashish4707/register-app.git'
            }
        }
        stage('Build Application'){
            steps{
                sh'mvn clean package'
            }
        }
        stage('Test Application'){
            steps{
                sh'mvn test'
            }
        }
        stage('SonaQube Analysis'){
            steps{
                withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-token') {
                    sh 'mvn sonar:sonar'
                }
            }
        }
    }

}
