pipeline {
    agent any

    environment {
        IMAGE_NAME = "1ms24mc020/my_maven_app"
        DOCKERHUB = credentials('dockerhub')
        KUBECONFIG_CRED = credentials('kubeconfig') // Jenkins credential for kubeconfig
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'git@github.com:Charitha1705/my_maven_app.git'
            }
        }

        stage('Build Maven Project') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:latest .'
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                sh 'docker login -u $DOCKERHUB_USR -p $DOCKERHUB_PSW'
                sh 'docker push $IMAGE_NAME:latest'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withKubeConfig([credentialsId: 'kubeconfig']) {
                    sh '''
                        kubectl set image deployment/my-maven-app my-maven-app=$IMAGE_NAME:latest --record
                        kubectl rollout status deployment/my-maven-app
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "Pipeline executed successfully"
        }
        failure {
            echo "Pipeline execution failed"
        }
        always {
            cleanWs()
        }
    }
}
