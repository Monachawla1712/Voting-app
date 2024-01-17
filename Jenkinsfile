// pipeline {
//     agent {
//         kubernetes {
//             yaml """
//             apiVersion: v1
//             kind: Pod
//             metadata:
//                 name: kaniko
//             spec:
//                 restartPolicy: Never
//                 containers:
//                 - name: kaniko
//                   image: gcr.io/kaniko-project/executor:debug
//                   command:
//                   - /busybox/cat
//                   tty: true
//             """
//         }
//     }

//     stages {
//         stage("Build docker image") {
//             steps {
//                 echo "Building the code"
//                 container('kaniko') {
//                     // Use Kaniko to build the Docker image
//                     sh "/kaniko/executor --dockerfile vote/Dockerfile --context . --destination=${env.dockerHubUser}/vote-app:latest"
//                     withCredentials([usernamePassword(credentialsId: "dockerHub", passwordVariable: "dockerHubPass", usernameVariable: "dockerHubUser")]) {
//                     // sh "docker tag Vote-app ${env.dockerHubUser}/vote-app:latest"
//                     sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
//                     sh "docker push ${env.dockerHubUser}/vote-app:latest"
//                 }
//             }
//         }

//         // stage("Push") {
//         //     steps {
//         //         echo "Pushing the docker images to Docker Hub"
//         //         withCredentials([usernamePassword(credentialsId: "dockerHub", passwordVariable: "dockerHubPass", usernameVariable: "dockerHubUser")]) {
//         //             // sh "docker tag Vote-app ${env.dockerHubUser}/vote-app:latest"
//         //             sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
//         //             sh "docker push ${env.dockerHubUser}/vote-app:latest"
//         //         }
//         //     }
//         // }
//     }
// }




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
        DOCKER_HUB_USER = credentials('dockerHub').username
        DOCKER_HUB_PASS = credentials('dockerHub').password
    }

    stages {
        stage("Build docker image") {
            steps {
                echo "Building the code"
                container('kaniko') {
                    // Use Kaniko to build the Docker image
                    sh "/kaniko/executor --dockerfile vote/Dockerfile --context . --destination=${DOCKER_HUB_USER}/vote-app:latest"
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
