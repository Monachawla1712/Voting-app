pipeline {
    agent any

    stages {
        stage("Code Clone") {
            agent {
                label 'docker-agent'
            }
            steps {
                echo "Cloning the app"
                git url: "https://github.com/Monachawla1712/Voting-app.git", branch: "main"
            }
        }

        stage("Build") {
            agent {
                label 'docker-agent'
            }
            steps {
                echo "Building the code"
                // Change to the template directory
                dir('vote/template') {
                    sh "docker build -t Vote-app ."
                }
            }
        }

        stage("Push") {
            agent {
                label 'docker-agent'
            }
            steps {
                echo "Pushing the docker images to Docker Hub"
                withCredentials([usernamePassword(credentialsId:"dockerHub", passwordVariable: "dockerHubPass", usernameVariable: "dockerHubUser")]){
                    sh "docker tag Vote-app ${env.dockerHubUser}/Vote-app:latest"
                    sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                    sh "docker push ${env.dockerHubUser}/Vote-app:latest"
                }
            }
        }
    }
}
