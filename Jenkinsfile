node {
    def app

    stage('Clone repository') {
        checkout scm
    }

    stage('Update GIT') {
        script {
            catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                withCredentials([usernamePassword(credentialsId: 'github', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                    sh """
                        echo "⚙️ Configuring Git..."
                        git config user.email "kaizeriftekar@gmail.com"
                        git config user.name "iftekarkaizer"

                        echo "📌 Switching to main branch..."
                        git checkout main

                        echo "⬇️ Pulling latest changes..."
                        git pull origin main --rebase

                        echo "🔍 Current deployment.yaml content:"
                        cat deployment.yaml

                        echo "🛠️ Updating image tag to: ${DOCKERTAG}"
                        sed -i 's|iftekar/test:.*|iftekar/test:${DOCKERTAG}|' deployment.yaml

                        echo "📄 Updated deployment.yaml:"
                        cat deployment.yaml

                        git add deployment.yaml
                        git commit -m "✅ Jenkins updated image tag to ${DOCKERTAG} in build #${BUILD_NUMBER}" || echo 'ℹ️ No changes to commit'

                        echo "🚀 Pushing changes to GitHub..."
                        git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${GIT_USERNAME}/kubernetesmanifest.git HEAD:main
                    """
                }
            }
        }
    }
}
