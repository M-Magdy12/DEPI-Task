pipeline {
    agent any
   
    stages {
        stage('build') {
            steps {
                sh """
                    docker build -t nginximage .
                """
            }
        }

        stage('push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dokcerhup', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh """
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker tag nginximage $DOCKER_USER/nginximage:latest
                        docker push $DOCKER_USER/nginximage:latest
                    """
                }
            }
        }

        stage('deploy') {
            steps {
                sshagent(credentials: ['key']) {
                    withCredentials([usernamePassword(credentialsId: 'dokcerhup', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh """
                            ssh -o StrictHostKeyChecking=no ubuntu@123.123.123.123 '
                                echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin &&
                                docker pull $DOCKER_USER/nginximage:latest &&
                                docker stop nginx-container || true &&
                                docker rm nginx-container || true &&
                                docker run -d --name nginx-container -p 80:80 $DOCKER_USER/nginximage:latest
                            '
                        """
                    }
                }
            }
        }
    }
}
