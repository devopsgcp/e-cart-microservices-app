pipeline {
    agent any
    stages {
        stage('Docker build images') {
            steps {
                sh '''
                  docker build -t adservice:v1 -f adservice/Dockerfile adservice
                  docker build -t cartservice:v1 -f cartservice/src/Dockerfile cartservice/src
                  docker build -t checkoutservice -f checkoutservice/ .
                  docker build -t checkoutservice -f currencyservice/ .
                  docker build -t checkoutservice -f emailservice/ .
                  docker build -t checkoutservice -f frontend/ .
                  docker build -t checkoutservice -f loadgenerator/ .
                  docker build -t checkoutservice -f paymentservice/ .
                  docker build -t checkoutservice -f productcatalogservice/ .
                  docker build -t checkoutservice -f recommendationservice/ .
                  docker build -t checkoutservice -f shippingservice/ .
                  docker build -t checkoutservice -f shoppingassistantservice/ .
                '''
            }
        }
    }
}
