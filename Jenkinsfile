
pipeline {
    agent any

    stages {
        stage('Build Docker Image') {
            steps {
                echo 'Build Docker Image'
                sh '/usr/local/bin/docker build -t week9-registration:v1 .'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    sh '''
                        echo "$DOCKER_PASS" | /usr/local/bin/docker login \
                            -u "$DOCKER_USER" --password-stdin
                    '''
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                sh '''
                    /usr/local/bin/docker tag week9-registration:v1 \
                        harinisundari/week9-registration:v1

                    /usr/local/bin/docker push \
                        harinisundari/week9-registration:v1
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                    /usr/local/bin/kubectl apply -f deployment.yaml
                    /usr/local/bin/kubectl apply -f service.yaml
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }

        failure {
            echo 'Pipeline failed. Please check the logs.'
        }
    }
}
