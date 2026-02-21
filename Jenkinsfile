pipeline {
    agent any
    stages {
        stage('Build Backend Image') {
            steps {
                sh 'docker rmi -f backend-app || true'
                sh 'docker build -t backend-app .'
            }
        }
        stage('Deploy Backend Containers') {
            steps {
                sh 'docker network create app-network || true'
                sh 'docker rm -f backend1 backend2 || true'
                sh 'docker run -d --name backend1 --network app-network backend-app'
                sh 'docker run -d --name backend2 --network app-network backend-app'
            }
        }
stage('Deploy NGINX Load Balancer') {
    steps {
        sh 'docker rm -f nginx-lb || true'
        sh 'docker run -d --name nginx-lb --network app-network -p 80:80 nginx'
        sh 'sleep 2'
        sh 'docker cp default.conf nginx-lb:/etc/nginx/conf.d/default.conf'
        sh 'docker exec nginx-lb nginx -s reload'
    }
}
    post {
        success {
            echo 'Pipeline executed successfully. NGINX load balancer is running.'
        }
        failure {
            echo 'Pipeline failed. Check console logs for errors.'
        }
    }
}
