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

    stages {
        stage("Build docker image") {
            steps {
                echo "Building the code"
                container('kaniko') {
                    // Use Kaniko to build the Docker image
                    withCredentials([usernamePassword(credentialsId: "dockerHub", passwordVariable: "DOCKER_HUB_PASS", usernameVariable: "DOCKER_HUB_USER")]) {
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
                        // Log in to Docker Hub using environment variables
                        sh "docker login -u ${DOCKER_HUB_USER} -p ${DOCKER_HUB_PASS}"
                        // Push the Docker image
                        sh "docker push ${DOCKER_HUB_USER}/vote-app:latest"
                    }
                }
            }
        }
    }
}
