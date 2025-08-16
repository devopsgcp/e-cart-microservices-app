pipeline {
    agent any
    stages {
        stage('Docker build images') {
            steps {
                sh '''
                  docker build -t adservice:v1 -f adservice/Dockerfile adservice
                  docker build -t cartservice:v1 -f cartservice/src/Dockerfile cartservice/src
                  docker build -t checkoutservice:v1 -f checkoutservice/Dockerfile checkoutservice
                  docker build -t currencyservice:v1 -f currencyservice/Dockerfile currencyservice
                  docker build -t emailservice:v1 -f emailservice/Dockerfile emailservice
                  docker build -t frontend:v1 -f frontend/Dockerfile frontend
                  docker build -t loadgenerator:v1 -f loadgenerator/Dockerfile loadgenerator
                  docker build -t paymentservice:v1 -f paymentservice/Dockerfile paymentservice
                  docker build -t productcatalogservice:v1 -f productcatalogservice/Dockerfile productcatalogservice
                  docker build -t recommendationservice:v1 -f recommendationservice/Dockerfile recommendationservice
                  docker build -t shippingservice:v1 -f shippingservice/Dockerfile shippingservice
                  docker build -t shoppingassistantservice:v1 -f shoppingassistantservice/Dockerfile shoppingassistantservice
                '''
            }
        }
    }
}
