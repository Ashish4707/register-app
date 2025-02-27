pipeline{
    agent {label 'Jenkins-Agent'}
    tools {
        jdk 'Java17'
        maven 'Maven3'
    }
    environment{
        APP_NAME = 'register-app-pipeline'
        RELEASE = '1.0.0'
        DOCKER_USER = 'ashish4707'
        DOCKER_PASS = 'dockerhub'
        IMAGE_NAME = "${DOCKER_USER}"+ "/" + "${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"

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
                echo 'Building Application...'
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
                withSonarQubeEnv(installationName: 'SonarQubeServer' ,credentialsId: 'jenkins-sonarqube-token') {
                    sh 'mvn sonar:sonar'
                }
            }
        }
        stage('Quality Gate'){
            steps{
                script{
                    waitForQualityGate abortPipeline: false , credentialsId: 'jenkins-sonarqube-token'
                }
            }
         }
        stage('Build and Push Docker Image'){
            steps{
                script{
                    docker.withRegistry('', DOCKER_PASS){
                        docker_image = docker.Build "${IMAGE_NAME}"  
                    }
                    docker.withRegistry('',DOCKER_PASS){
                        docker_image.Push("${MAGE_TAG}")
                        docker_image.Push("latest")
                    }
                }
            }
        }

        
    }

}
