pipeline {
    agent any
    environment {
        DOCKER_IMAGE_NAME = "<4aditi4/metadata-service"
        DOCKER_USERNAME = "4aditi4"
        DOCKER_PASSWORD = credentials('DOCKER_SECRET')
    }
    stages {
        stage('Test') {
            steps {
                sh '''
                    echo "running the tests ......."
                    mvn test
                '''
            }
        }
        stage('Build') {
            steps {
                sh '''
                    echo "building the docker image ......."
                    docker build -t "${DOCKER_IMAGE_NAME}" .
                '''
            }
        }
        stage('Push') {
            steps {
                sh '''
                    echo "pushing docker image ......."
                    docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}
                    docker tag "${DOCKER_IMAGE_NAME}" "${DOCKER_IMAGE_NAME}":"$BUILD_NUMBER"
                    docker push "${DOCKER_IMAGE_NAME}":"$BUILD_NUMBER"
                    docker push "${DOCKER_IMAGE_NAME}":latest
                    echo "cleaning up the local images ......."
                    docker rmi "${DOCKER_IMAGE_NAME}":"$BUILD_NUMBER"
                    docker rmi "${DOCKER_IMAGE_NAME}":latest
                '''
            }
        }
        stage('Deploy') {
            steps {
                sh '''
                    echo "deploying the application ........"
                    docker rm -f dobby-app || true
                    docker run -d -p 4444:4444 --name dobby-app "${DOCKER_IMAGE_NAME}":latest
                '''
            }
        }
    }
}