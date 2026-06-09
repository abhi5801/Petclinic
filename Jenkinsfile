pipeline {
    agent any

    environment {
        DOCKER_HUB_USER = 'abhi1222'
        COMPOSE_PROJECT_NAME = 'petclinic'
        JAVA_HOME = '/usr/lib/jvm/java-17-openjdk-amd64'
        PATH = "${JAVA_HOME}/bin:${env.PATH}"
    }

    tools {
        maven 'Maven 3'
        jdk 'JDK17'

    }

    stages {

        stage('Check Java') {
    steps {
        sh '''
        echo $JAVA_HOME
        java -version
        javac -version
        mvn -version
        '''
    }
}
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/abhi5801/Petclinic.git'
            }
        }

        stage('Build Maven Project') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'abhi1222',
                        passwordVariable: 'Abhi@1222'
                    )
                ]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$abhi1222" --password-stdin
                    '''
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                sh 'docker compose build'
            }
        }

        stage('Tag Images') {
            steps {
                sh '''
                    docker tag petclinic-config-server:latest $DOCKER_HUB_USER/petclinic-config-server:${BUILD_NUMBER}
                    docker tag petclinic-discovery-server:latest $DOCKER_HUB_USER/petclinic-discovery-server:${BUILD_NUMBER}
                    docker tag petclinic-api-gateway:latest $DOCKER_HUB_USER/petclinic-api-gateway:${BUILD_NUMBER}

                    docker tag petclinic-config-server:latest $DOCKER_HUB_USER/petclinic-config-server:latest
                    docker tag petclinic-discovery-server:latest $DOCKER_HUB_USER/petclinic-discovery-server:latest
                    docker tag petclinic-api-gateway:latest $DOCKER_HUB_USER/petclinic-api-gateway:latest
                '''
            }
        }

        stage('Push Images') {
            steps {
                sh '''
                    docker push $DOCKER_HUB_USER/petclinic-config-server:${BUILD_NUMBER}
                    docker push $DOCKER_HUB_USER/petclinic-config-server:latest

                    docker push $DOCKER_HUB_USER/petclinic-discovery-server:${BUILD_NUMBER}
                    docker push $DOCKER_HUB_USER/petclinic-discovery-server:latest

                    docker push $DOCKER_HUB_USER/petclinic-api-gateway:${BUILD_NUMBER}
                    docker push $DOCKER_HUB_USER/petclinic-api-gateway:latest
                '''
            }
        }
    }

    post {
        always {
            sh 'docker logout || true'
        }

        success {
            echo 'Build and Docker Push completed successfully'
        }

        failure {
            echo 'Pipeline failed'
        }
    }
}
