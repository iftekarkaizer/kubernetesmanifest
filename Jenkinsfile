pipeline {
    agent any

    environment {
        DOCKERTAG = "${env.BUILD_NUMBER}"  // or another tag strategy
        IMAGE_NAME = "iftekar/test"
        GITHUB_REPO = "iftekarkaizer/kubernetesmanifest.git"
        GIT_CREDENTIALS_ID = 'github' // your Jenkins GitHub credentials ID
        DOCKER_CREDENTIALS_ID = 'dockerhub' // your Docker Hub credentials ID
    }

    stages {
        stage('Clone repository') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker image') {
            steps {
                script {
                    docker.withRegistry('', "${DOCKER_CREDENTIALS_ID}") {
                        def image = docker.build("${IMAGE_NAME}:${DOCKERTAG}")
                        image.push()
                    }
                }
            }
        }

        stage('Update deployment.yaml and push') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: "${GIT_CREDENTIALS_ID}", usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                        sh """
                            git config user.email "kaizeriftekar@gmail.com"
                            git config user.name "iftekarkaizer"
                            git checkout main
                            git pull origin main --rebase
                            sed -i 's|${IMAGE_NAME}:.*|${IMAGE_NAME}:${DOCKERTAG}|' deployment.yaml
                            git add deployment.yaml
                            git commit -m "Jenkins: Update image tag to ${DOCKERTAG} (build #${env.BUILD_NUMBER})" || echo "No changes to commit"
                            git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${GIT_USERNAME}/${GITHUB_REPO} HEAD:main
                        """
                    }
                }
            }
        }
    }
}


