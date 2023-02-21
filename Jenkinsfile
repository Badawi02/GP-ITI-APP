pipeline {
    agent { label 'slave' }

    stages {
        stage('build') {
            steps {
                script {
                       withCredentials([usernamePassword(credentialsId: 'docker_hub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                           sh """
                                docker login -u $USERNAME -p $PASSWORD
                                docker build -t ahmedbadawi/jenkinsproject:${BUILD_NUMBER} .
                                docker push ahmedbadawi/jenkinsproject:${BUILD_NUMBER}
                                echo ${BUILD_NUMBER} > ../bakehouse-build-number.txt
                           """
                       }
                }
            }
        }
        stage('deploy') {
            steps {
                script {
                            withCredentials([file(credentialsId: 'kubernetes_kubeconfig', variable: 'KUBECONFIG')]) {
                          sh """
                              export BUILD_NUMBER=\$(cat ../bakehouse-build-number.txt)
                              mv Deployment/deploy.yaml Deployment/deploy.yaml.tmp
                              cat Deployment/deploy.yaml.tmp | envsubst > Deployment/deploy.yaml
                              rm -f Deployment/deploy.yaml.tmp
                              kubectl apply -f Deployment --kubeconfig=${KUBECONFIG}
                            """
                        }
                }
            }
        }
    }
}
