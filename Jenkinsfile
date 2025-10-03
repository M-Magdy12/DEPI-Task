pipeline {
    agent any
   
    stages {
        stage('build') {
            steps {
                sh """
                    docker build -t  nginximage .
                    docker images
                """
            }
        }
    }
}
