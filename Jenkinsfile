pipeline {
agent any

environment {
    DOCKER_CONFIG = 'C:/Users/VARSHITHA/.docker'
    KUBECONFIG = 'C:/Users/VARSHITHA/.kube/config'
}

stages {
    stage('Install Dependencies') {
        steps {
            echo "Installing Python dependencies"
            bat '"C:/Users/VARSHITHA/AppData/Local/Python/pythoncore-3.14-64/python.exe" -m pip install -r requirements.txt'
        }
    }

    stage('Start Flask Application') {
        steps {
            echo "Starting Flask application"
            bat 'start "FlaskApp" /B "C:/Users/VARSHITHA/AppData/Local/Python/pythoncore-3.14-64/python.exe" app.py'
            bat 'ping 127.0.0.1 -n 6 > nul'
        }
    }

    stage('Run Selenium Tests') {
        steps {
            echo "Running Selenium tests"
            bat '"C:/Users/VARSHITHA/AppData/Local/Python/pythoncore-3.14-64/python.exe" -m pytest test -v'
        }
    }

    stage('Build Docker Image') {
        steps {
            echo "Checking Docker Desktop context"
            bat 'docker --context desktop-linux version'
            echo "Building Docker image"
            bat 'docker --context desktop-linux build -t varshithaa777/registration-form:latest .'
        }
    }

    stage('Docker Login') {
        steps {
            echo "Logging into Docker Hub"
            withCredentials([
                usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKER_USERNAME',
                    passwordVariable: 'DOCKER_PASSWORD'
                )
            ]) {
                bat 'echo %DOCKER_PASSWORD%| docker --context desktop-linux login -u "%DOCKER_USERNAME%" --password-stdin'
            }
        }
    }

    stage('Push Docker Image') {
        steps {
            echo "Pushing Docker image"
            bat 'docker --context desktop-linux push varshithaa777/registration-form:latest'
        }
    }

    stage('Deploy to Kubernetes') {
        steps {
            echo "Deploying to Kubernetes"
            bat 'kubectl apply -f deployment.yaml'
            bat 'kubectl apply -f service.yaml'
        }
    }

    stage('Verify Deployment') {
        steps {
            echo "Checking Kubernetes resources"
            bat 'kubectl get deployments'
            bat 'kubectl get pods'
            bat 'kubectl get services'
        }
    }
}

post {
    success {
        echo 'Pipeline completed successfully!'
    }
    failure {
        echo 'Pipeline failed. Please check the console output.'
    }
}


}
