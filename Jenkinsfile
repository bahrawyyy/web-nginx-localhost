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
                sshagent(['ec2-server-key']) {
                    script {
                        withCredentials([usernamePassword(credentialsId: 'docker-hub-repo', usernameVariable: 'dockerhub_username', passwordVariable: 'dockerhub_password')]) {
                            sh """
                            ansible-playbook -i inventory ${ANSIBLE_PLAYBOOK} --extra-vars "@vars.yaml"
                            """
                        }
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
