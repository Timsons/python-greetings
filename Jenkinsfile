pipeline {
    agent any
    stages{
        stage('build-docker-image') {
            steps {
                script{
                    buildImage()
                }
            }
        }
        stage('deploy-to-dev') {
            steps {
                deploy("DEV")
            }
        }
        stage('tests-on-dev') {
            steps {
                test("DEV")
            }
        }
        stage('deploy-to-stg') {
            steps {
                deploy("STG")
            }
        }
        stage('tests-on-stg') {
            steps {
                test("STG")
            }
        }
        stage('deploy-to-prod') {
            steps {
                deploy("PROD")
            }
        }
        stage('tests-on-prod') {
            steps {
                test("PROD")
            }
        }
    }
}

def buildImage(){
    echo "Building of Python app is starting..."
    sh "docker build -t tsnetkovs/python-greetings-app:latest ."
    echo "Pushing the built image to docker registry..."
    sh "docker push tsnetkovs/python-greetings-app:latest"
}

def deploy(String environment){
    echo "Deployment of Python app to ${environment} environment is starting..."
    echo "Pulling the latest version of Python app from dockerhub..."
    sh "docker pull tsnetkovs/python-greetings-app:latest"
    echo "Stopping docker service..."
    sh "docker compose stop greetings-app-${environment.toLowerCase()}"
    echo "Removing docker service..."
    sh "docker compose rm -sf greetings-app-${environment.toLowerCase()}"
    //fails for me on up step, I can try the down command...
    sh "docker compose down greetings-app-${environment.toLowerCase()}"
    echo "Creating docker service..."
    sh "docker compose up -d greetings-app-${environment.toLowerCase()}"
}

def test(String environment){
    echo "API test execution against Python app on ${environment} environment is starting..."
    sh "docker pull tsnetkovs/api-tests:latest"
    sh "docker run --network=host --rm tsnetkovs/api-tests:latest run greetings greetings_${environment.toLowerCase()}"
}