pipeline {
    agent { label "Jenkins-Agent" }

    environment {
        APP_NAME  = "register-app-pipeline"
        GIT_REPO  = "gitops-register-app"
        GIT_BRANCH = "main"
    }

    stages {

        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Checkout GitOps Repo") {
            steps {
                git branch: "${GIT_BRANCH}",
                    credentialsId: 'github-cred',
                    url: "https://github.com/pratyush934/${GIT_REPO}.git"
            }
        }

        stage("Update Deployment Image Tag") {
            steps {
                sh """
                  echo "Before update:"
                  cat deployment.yaml

                  sed -i 's|image: ${APP_NAME}:.*|image: ${APP_NAME}:${IMAGE_TAG}|' deployment.yaml

                  echo "After update:"
                  cat deployment.yaml
                """
            }
        }

        stage("Commit & Push Changes") {
            steps {
                sh """
                  git config user.name "pratyush934"
                  git config user.email "pratyushsinha982@gmail.com"
                  git add deployment.yaml
                  git commit -m "chore: update image tag to ${IMAGE_TAG}" || echo "No changes to commit"
                """

                withCredentials([gitUsernamePassword(credentialsId: 'github-cred', gitToolName: 'Default')]) {
                    sh """
                      git push origin ${GIT_BRANCH}
                    """
                }
            }
        }
    }
}
