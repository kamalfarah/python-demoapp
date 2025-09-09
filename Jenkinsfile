pipeline {
    agent any

    environment {
        IMAGE_NAME   = "kamalfarah/python-demoapp"
        IMAGE_TAG    = "python-app-1.0"
        REGISTRY_USER = "kamalfarah"
        REGISTRY_PASS = "Kmeva1982"
    }

    stages {
        stage('Test') {
            agent {
                docker {
                    image 'python:3.9-slim-buster'
                    args '-u root' // allow apt-get
                }
            }
            steps {
                sh '''
                    apt-get update
                    apt-get install -y make
                    make test
                '''
            }
        }

        stage('Build & Push Image') {
            agent {
                docker {
                    image 'docker:20.10.16'
                    args '--privileged -v /var/run/docker.sock:/var/run/docker.sock'
                }
            }
            steps {
                withEnv(["DOCKER_TLS_CERTDIR=/certs"]) {
                    sh '''
                        echo $REGISTRY_PASS | docker login -u $REGISTRY_USER --password-stdin
                        docker build -t $IMAGE_NAME:$IMAGE_TAG .
                        docker push $IMAGE_NAME:$IMAGE_TAG
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig-demo', variable: 'KUBECONFIG')]) {
                    sh '''
                        set -e
                        # Apply manifests (if first time)
                        kubectl -n demo apply -f kubernetes/

                        # Update the running deployment to new image tag
                        kubectl -n demo set image deployment/python-demoapp python-demoapp=$IMAGE_NAME:$IMAGE_TAG

                        # Wait for rollout to complete
                        kubectl -n demo rollout status deployment/python-demoapp
                    '''
                }
            }
        }
    }
}
