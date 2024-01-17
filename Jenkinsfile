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
                    sh '/kaniko/executor --dockerfile vote/Dockerfile --context . --destination=vote-app:latest'
                }
            }
        }

        stage("Push") {
            steps {
                echo "Pushing the docker images to Docker Hub"
                withCredentials([usernamePassword(credentialsId: "dockerHub", passwordVariable: "dockerHubPass", usernameVariable: "dockerHubUser")]) {
                    sh "docker tag Vote-app ${env.dockerHubUser}/Vote-app:latest"
                    sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                    sh "docker push ${env.dockerHubUser}/Vote-app:latest"
                }
            }
        }
    }
}
