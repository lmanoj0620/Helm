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
                '''
            }
        }
    }
}
