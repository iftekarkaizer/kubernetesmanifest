node {
    stage('Clone repository') {
        checkout scm
    }

    stage('Update GIT') {
        script {
            catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                withCredentials([usernamePassword(credentialsId: 'github', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                    sh '''
                        git config user.email "kaizeriftekar@gmail.com"
                        git config user.name "iftekarkaizer"

                        echo "Showing current deployment.yaml:"
                        cat deployment.yaml

                        echo "Updating image tag to ${DOCKERTAG}..."
                        sed -i "s+iftekar/test:.*+iftekar/test:${DOCKERTAG}+g" deployment.yaml

                        echo "Updated deployment.yaml:"
                        cat deployment.yaml

                        git add deployment.yaml
                        git commit -m "Jenkins: Updated image tag to ${DOCKERTAG} (build #${BUILD_NUMBER})" || echo "No changes to commit"
                        git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${GIT_USERNAME}/kubernetesmanifest.git HEAD:main
                    '''
                }
            }
        }
    }
}
