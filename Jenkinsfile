pipeline {
    agent any
    stages {
        stage('Docker build images') {
            steps {
                sh '''
                  docker build -t adservice:v1 -f adservice/Dockerfile adservice
                  docker build -t cartservice:v1 -f cartservice/src/Dockerfile cartservice/src
                '''
            }
        }
    }
}
