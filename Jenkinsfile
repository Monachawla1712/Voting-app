pipeline {
    agent {
        kubernetes {
            yaml """
            apiVersion: v1
            kind: Pod
            metadata:
                name: kaniko
            spec:
                restartPolicy: Never
                containers:
                - name: kaniko
                  image: gcr.io/kaniko-project/executor:debug
                  command:
                  - /busybox/cat
                  tty: true
            """
        }
    }

    environment {
        DOCKER_HUB_CREDENTIALS = credentials('dockerHub')
    }

    stages {

        stage("Code Clone") {
            steps {
                echo "Cloning the app"
                git url: "https://github.com/Monachawla1712/Voting-app.git", branch: "main"
            }
        }
        
        stage("Build docker image") {
            steps {
                echo "Building the code"
                container('kaniko') {
                    // Use Kaniko to build the Docker image
                    withCredentials([usernamePassword(credentialsId: DOCKER_HUB_CREDENTIALS, passwordVariable: 'DOCKER_HUB_PASS', usernameVariable: 'DOCKER_HUB_USER')]) {
                        sh "/kaniko/executor --dockerfile vote/Dockerfile --context . --destination=${DOCKER_HUB_USER}/vote-app:latest"
                    }
                }
            }
        }

        stage("Push") {
            steps {
                echo "Pushing the docker images to Docker Hub"
                container('kaniko') {
                    // Use the same Kaniko container for consistency
                    script {
                        // Log in to Docker Hub using Jenkins credentials
                        sh "echo \$DOCKER_HUB_PASS | docker login -u \$DOCKER_HUB_USER --password-stdin"
                        // Push the Docker image
                        sh "docker push ${DOCKER_HUB_USER}/vote-app:latest"
                    }
                }
            }
        }
    }
}
