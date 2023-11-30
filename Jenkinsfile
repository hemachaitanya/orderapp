pipeline {
    agent { label 'EEE'}
    triggers { pollSCM ('* * * * *') }
    stages {
        stage('checkout') {
            steps {
                git url: 'https://github.com/hemachaitanya/orderapp.git',
                    branch: 'main'
                }
        }
        stage ('Build and push docker image') {
            steps {
                sh "docker image build -t hema789/argocdrepo:sb_${BUILD_ID} ."
                sh "docker image push hema789/argocdrepo:sb_${BUILD_ID}"
            }
        }
        stage ('update k8s manifest') {
            steps {
                sh "cd ~/orderops && yq eval -i '.spec.template.spec.containers[0].image= \"hema789/argocdrepo:sb_${BUILD_ID}\" ' ~/orderops/manifests/orderdeploy.yaml"
                sh """
                  cd ~/orderops
                  git add ~/orderops/manifests/orderdeploy.yaml
                  git commit -m "added new change"
                  git push origin main
                """
            }
        }
        
    }
}
