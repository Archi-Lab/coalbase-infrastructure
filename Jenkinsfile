pipeline {

    agent any

    stages{
        stage('Startup Logging'){
            steps {
                script {
                    docker.withServer('tcp://10.10.10.25:2376', 'CoalbaseVM') {
                        sh 'docker stack deploy -c logging/docker-compose.yml logging'
                    }
                }
            }
        }
    }

}
