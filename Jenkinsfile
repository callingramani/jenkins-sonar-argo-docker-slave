pipeline{
    
    agent{
        label 'gcp_e2_standard_2'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: '76ab3bc1-0647-458c-a50a-5e99e78e3b6f', url: 'git@github.com:callingramani/jenkins-sonar-argo-docker-slave.git']])
            }
        }
        stage('Build & Test') {
            steps {
                sh 'ls -la'
                sh 'java -version'
                sh 'mvn -version'
                sh 'mvn clean package'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube-server') {
                    sh 'mvn sonar:sonar'
                }
            }
        }
        stage('Build & Push Docker Image') {
            environment {
                DOCKER_IMAGE = "ankramani/argo-jenkins-sonar:${env.BUILD_NUMBER}"
            }
            steps {
                withDockerRegistry(credentialsId: 'jenkins-dockerhub-private', url: 'https://index.docker.io/v1/') {
                    sh '''
                        docker build -t $DOCKER_IMAGE .
                        docker push $DOCKER_IMAGE
                    '''
                }
            }
         }
    }      
}
