node {
    stage('Checkout') {
        checkout scm
    }

    stage('Build Grafana') {
        sh "docker build -t docker.nexus.archi-lab.io/archilab/grafana-custom -f monitoring/dockerfile.grafana.yaml ."
        sh "docker tag docker.nexus.archi-lab.io/archilab/grafana-custom docker.nexus.archi-lab.io/archilab/grafana-custom:${env.BUILD_ID}"
        docker.withRegistry('https://docker.nexus.archi-lab.io//', 'archilab-nexus-jenkins') {
            sh "docker push docker.nexus.archi-lab.io/archilab/grafana-custom"
            sh "docker push docker.nexus.archi-lab.io/archilab/grafana-custom:${env.BUILD_ID}"
        }
    }

    stage('Build Prometheus') {
        sh "docker build -t docker.nexus.archi-lab.io/archilab/prometheus-custom -f monitoring/dockerfile.prometheus.yaml ."
        sh "docker tag docker.nexus.archi-lab.io/archilab/prometheus-custom docker.nexus.archi-lab.io/archilab/prometheus-custom:${env.BUILD_ID}"
        docker.withRegistry('https://docker.nexus.archi-lab.io//', 'archilab-nexus-jenkins-user') {
            sh "docker push docker.nexus.archi-lab.io/archilab/prometheus-custom"
            sh "docker push docker.nexus.archi-lab.io/archilab/prometheus-custom:${env.BUILD_ID}"
        }
    }
    stage('Startup Infrastructure') {
        docker.withServer('tcp://10.10.10.51:2376', 'coalbase-prod-certs') {
            sh 'docker stack deploy -c logging/docker-compose.yml logging'
            docker.withRegistry('https://docker.nexus.archi-lab.io//', 'archilab-nexus-jenkins') {
                withCredentials([usernamePassword(credentialsId: 'archilab-grafana-admin',
                        usernameVariable: 'GF_SECURITY_ADMIN_USER',
                        passwordVariable: 'GF_SECURITY_ADMIN_PASSWORD')]) {
                    sh 'docker stack deploy --with-registry-auth -c monitoring/docker-compose.yaml monitoring'
                }
            }
        }
    }
}
