pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "mrahul19/abc-technologies"
        DOCKER_TAG = "${BUILD_NUMBER}"
        DOCKER_CREDENTIALS = 'docker-hub-credentials'
    }

    triggers {
        githubPush()
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Unit Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }

        stage('Package') {
            steps {
                sh 'mvn package -DskipTests'
            }
        }

        stage('Code Analysis') {
            steps {
                sh 'mvn checkstyle:check || true'
            }
        }

        stage('Docker Build') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                sh "docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_IMAGE}:latest"
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${DOCKER_CREDENTIALS}", usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh "echo $PASS | docker login -u $USER --password-stdin"
                    sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
                    sh "docker push ${DOCKER_IMAGE}:latest"
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh "kubectl apply -f k8s/k8s-deployment.yml"
                sh "kubectl apply -f k8s/k8s-service.yml"
                sh "kubectl set image deployment/devops-app devops-app=${DOCKER_IMAGE}:${DOCKER_TAG} -n devops"
            }
        }

        stage('Verify Deployment') {
            steps {
                sh 'kubectl rollout status deployment/devops-app -n devops --timeout=120s'
                sh 'kubectl get pods -n devops'
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check logs for details.'
        }
        always {
            cleanWs()
        }
    }
}
