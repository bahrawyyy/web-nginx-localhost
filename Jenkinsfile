pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = credentials('docker-hub-repo') 
        ANSIBLE_PLAYBOOK = 'ansible-playbook.yaml' 
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Run Ansible Playbook') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-repo', usernameVariable: 'dockerhub_username', passwordVariable: 'dockerhub_password')]) {
                        sh """
                        ansible-playbook ${ANSIBLE_PLAYBOOK} --extra-vars "@vars.yaml"
                        """
                    }
                }
            }
        }
    }

    post {
        always {
            script {
                sh "docker image prune -f"
            }
        }
    }
}
