pipeline {
    agent any 
    environment {
        KUBECONFIG = "/var/lib/jenkins/.kube/config"  // Ensure Jenkins uses the correct kubeconfig
    }
    
    stages {
        stage("Clone Code") {
            steps {
                echo "Cloning the code"
                git url: "https://github.com/prathamnandgirwar/Node-js.git", branch: "main"
            }
        }

        stage("Build") {
            steps {
                echo "Building the image"
                sh "docker build -t my-note-app ."
            }
        }

        stage("Push to Docker Hub") {
            steps {
                echo "Pushing the image to Docker Hub"
                withCredentials([usernamePassword(credentialsId: "docker", passwordVariable: "dockerHubPass", usernameVariable: "dockerHubUser")]) {
                    sh "docker tag my-note-app ${dockerHubUser}/my-note-app:latest"
                    sh "echo ${dockerHubPass} | docker login --username ${dockerHubUser} --password-stdin"
                    sh "docker push ${dockerHubUser}/my-note-app:latest"
                }
            }
        }

        stage("Deploy") {
            steps {
                echo "Deploying the container"
                sh "kubectl apply -f deployment.yaml --kubeconfig=/var/lib/jenkins/.kube/config"
                sh "kubectl apply -f service.yaml --kubeconfig=/var/lib/jenkins/.kube/config"
            }
        }
    }
}
