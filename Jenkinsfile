pipeline {

    agent any

    stages{
        stage('Build') {
                steps {
                    updateGitlabCommitStatus name: "Building", state: "running"
                    sh "docker build -t docker.nexus.archi-lab.io/archilab/grafana-custom -f monitoring/dockerfile.grafana.yaml ."
                    sh "docker tag docker.nexus.archi-lab.io/archilab/grafana-custom docker.nexus.archi-lab.io/archilab/grafana-custom:${env.BUILD_ID}"
                    script {
                        docker.withRegistry('https://docker.nexus.archi-lab.io//', 'archilab-nexus-jenkins-user') {
                            sh "docker push docker.nexus.archi-lab.io/archilab/grafana-custom"
                        }
                    }
                    sh "docker build -t docker.nexus.archi-lab.io/archilab/prometheus-custom -f monitoring/dockerfile.grafana.yaml ."
                    sh "docker tag docker.nexus.archi-lab.io/archilab/prometheus-custom docker.nexus.archi-lab.io/archilab/prometheus-custom:${env.BUILD_ID}"
                    script {
                          docker.withRegistry('https://docker.nexus.archi-lab.io//', 'archilab-nexus-jenkins-user') {
                              sh "docker push docker.nexus.archi-lab.io/archilab/prometheus-custom"
                          }
                    }
                }
                post {
                  success {
                    updateGitlabCommitStatus name: "Building", state: "success"
                  }
                  failure {
                    updateGitlabCommitStatus name: "Building", state: "failed"
                  }
                  unstable {
                    updateGitlabCommitStatus name: "Building", state: "success"
                  }
                }
        }
        stage('Startup Infrastructure'){
            steps {
                script {
                    docker.withServer('tcp://10.10.10.25:2376', 'CoalbaseVM') {
                        sh 'docker stack deploy -c logging/docker-compose.yml logging'
                        docker.withRegistry('https://docker.nexus.archi-lab.io//', 'archilab-nexus-jenkins-user') {
                          withCredentials([usernamePassword(credentialsId: 'GrafanaCredentials', usernameVariable: 'GF_SECURITY_ADMIN_USER', passwordVariable: 'GF_SECURITY_ADMIN_PASSWORD')]){
                            sh 'docker stack deploy --with-registry-auth -c monitoring/docker-compose.yaml monitoring'
                           }
                        }
                    }
                }
            }
        }
    }

}
