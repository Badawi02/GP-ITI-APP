pipeline {
    agent { label 'slave' }

    stages {
        stage('build') {
            steps {
                script {
                       withCredentials([usernamePassword(credentialsId: 'docker_hub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                           sh """
                                docker login -u $USERNAME -p $PASSWORD
                                docker build -t ahmedbadawi/finalproject:${BUILD_NUMBER} .
                                docker push ahmedbadawi/finalproject:${BUILD_NUMBER}
                                echo ${BUILD_NUMBER} > ../python_app.txt
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
                              export BUILD_NUMBER=\$(cat ../python_app.txt)
                              mv deploymentFiles-app/deploy_app.yml deploymentFiles-app/deploy_app.yml.tmp
                              cat deploymentFiles-app/deploy_app.yml.tmp | envsubst > deploymentFiles-app/deploy_app.yml
                              rm -f deploymentFiles-app/deploy_app.yml.tmp
                              kubectl apply -f deploymentFiles-app --kubeconfig=${KUBECONFIG}
                            """
                        }
                }
            }
        }
    }
}
