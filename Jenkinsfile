pipeline {
    agent any

    parameters {
        choice(
            name: 'SERVICE',
            choices: [
                'all',
                'adservice',
                'cartservice',
                'checkoutservice',
                'currencyservice',
                'emailservice',
                'frontend',
                'loadgenerator',
                'paymentservice',
                'productcatalogservice',
                'recommendationservice',
                'shippingservice',
                'shoppingassistantservice'
            ],
            description: 'Select which service to build & push (choose "all" for every service)'
        )
        choice(
            name: 'IMAGE_TAG', 
            choices: ['latest','v1','v2'], 
            description: 'Docker image tag (e.g. v1, latest, commit hash)')
    }

    environment {
        PROJECT_ID = 'cicd-2024'
        REGION = 'asia-south2'
        REPO_NAME = 'e-cart-app'
        ARTIFACT_REGISTRY = "${REGION}-docker.pkg.dev"
        IMAGE_TAG        = "${params.IMAGE_TAG}"
    }

    stages {
        stage('Docker Build Images') {
            steps {
                script {
                    def services = []
                    if (params.SERVICE == "all") {
                        services = [
                            "adservice",
                            "cartservice",
                            "checkoutservice",
                            "currencyservice",
                            "emailservice",
                            "frontend",
                            "loadgenerator",
                            "paymentservice",
                            "productcatalogservice",
                            "recommendationservice",
                            "shippingservice",
                            "shoppingassistantservice"
                        ]
                    } else {
                        services = [params.SERVICE]
                    }

                    services.each { service ->
                        echo "🔨 Building Docker image for ${service}"
                        sh """
                            docker build -t ${service}:${IMAGE_TAG} -f ${service}/Dockerfile ${service} || \
                            docker build -t ${service}:${IMAGE_TAG} -f ${service}/src/Dockerfile ${service}/src
                        """
                    }
                }
            }
        }

        stage('Trivy Scan') {
            steps {
                script {
                    def services = []
                    if (params.SERVICE == "all") {
                        services = [
                            "adservice",
                            "cartservice",
                            "checkoutservice",
                            "currencyservice",
                            "emailservice",
                            "frontend",
                            "loadgenerator",
                            "paymentservice",
                            "productcatalogservice",
                            "recommendationservice",
                            "shippingservice",
                            "shoppingassistantservice"
                        ]
                    } else {
                        services = [params.SERVICE]
                    }

                    services.each { service ->
                        echo "🔍 Running Trivy scan for ${service}"
                        sh """
                            trivy image --severity HIGH,CRITICAL --format json \
                              -o trivy-${service}-report.json ${service}:${IMAGE_TAG} || true
                        """
                    }
                }
                archiveArtifacts artifacts: 'trivy-*.json', allowEmptyArchive: true
            }
        }
        
        
        stage('Set up GCP Auth') {
            steps {
                withCredentials([file(credentialsId: 'gcp-sa-key', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
                    sh '''
                        echo "🔑 Activating service account..."
                        gcloud auth activate-service-account --key-file=$GOOGLE_APPLICATION_CREDENTIALS
                        gcloud config set project $PROJECT_ID
                        gcloud auth configure-docker $ARTIFACT_REGISTRY --quiet
                    '''
                }
            }
        }

        stage('Tag & Push Images to Artifact Registry') {
            steps {
                script {
                    def services = []
                    if (params.SERVICE == "all") {
                        services = [
                            "adservice",
                            "cartservice",
                            "checkoutservice",
                            "currencyservice",
                            "emailservice",
                            "frontend",
                            "loadgenerator",
                            "paymentservice",
                            "productcatalogservice",
                            "recommendationservice",
                            "shippingservice",
                            "shoppingassistantservice"
                        ]
                    } else {
                        services = [params.SERVICE]
                    }

                    services.each { service ->
                        echo "📦 Tagging and pushing ${service}..."
                        sh """
                            docker tag ${service}:${IMAGE_TAG} $ARTIFACT_REGISTRY/$PROJECT_ID/$REPO_NAME/${service}:${IMAGE_TAG}
                            docker push $ARTIFACT_REGISTRY/$PROJECT_ID/$REPO_NAME/${service}:${IMAGE_TAG}
                        """
                    }
                }
            }
        }

        stage ('Deploy to gke cluster')
    }
}
