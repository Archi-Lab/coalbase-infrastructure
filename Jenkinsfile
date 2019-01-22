pipeline {

    agent any

    stages{
        stage('Startup Logging'){
            steps {
                script {
                    docker.withServer('tcp://10.10.10.25:2376', 'CoalbaseVM') {
                        sh 'docker stack deploy -c logging/docker-compose.yml logging'
                        withCredentials([usernamePassword(credentialsId: 'GrafanaCredentials', usernameVariable: 'GF_SECURITY_ADMIN_USER', passwordVariable: 'GF_SECURITY_ADMIN_PASSWORD')]){
                          sh 'docker stack deploy -c monitoring/docker-compose.yaml monitoring'
                         }
                    }
                }
            }
        }
    }

}
