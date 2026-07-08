pipeline {
    agent any 

    environment {
        Image_Name = "lmanojbalaji/manojbala:${GIT_COMMIT}"
    }
    stages {
        stage('Git-checkout') {
            steps {
                git url: 'https://github.com/lmanoj0620/Helm.git', branch: 'main'
            }
        }
        stage('Building-Stage') {
            steps {
                sh '''
                    printenv
                    docker build -t ${Image_Name} .
                '''
            }
        }
        stage("Docker-Login") {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'docker-hub-creds',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin'
                }
            }
        }
        stage('Pushing to Docker hub') {
            steps {
                sh '''
                    docker push ${Image_Name}
                    echo "${Image_Name} pushed to Docker Hub successfully."
                '''
            } 
        }
        stage("install Microk8s") {
            steps {
                sh """ snap install microk8s --classic
                        microk8s start
                        alias kubectl="microk8s kubectl"
                    """
                }
        }
        stage("install Helm") {
            steps {
                sh """ alias helm="microk8s helm3"
                        helm version 
                        helm create my-app
                        helm install my-app-release ./my-app \
                        --set image.repository=lmanojbalaji/manojbala \
                        --set image.tag=${GIT_COMMIT} \
                        --set service.type=NodePort \
                        --set service.port=8501

                        
                    """
                }               
        }
        stage("Check Deployments"){
            steps {
                sh """ microk8s.kubectl get deployments
                        microk8s.kubectl get pods
                    """
                }
        }
    }
}
