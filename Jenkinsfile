pipeline {
    agent any

    environment {
        ANSIBLE_PLAYBOOK = 'ansible-playbook.yaml'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm  // Checkout the repository with the playbook
                 sh "git clone https://github.com/aaabaza761/wen-nginx-localhost.git "
            }
        }

        stage('Run Ansible Playbook for Kubernetes') {
            steps {
                script {
                    // Run the Ansible playbook to deploy on the Kubernetes cluster
                    sh """
                    ansible-playbook -i localhost  ${ANSIBLE_PLAYBOOK} "
                    """
                }
            }
        }
    }

    post {
        always {
            // Clean up unused Docker images to save space
            script {
                      sh "docker image prune -f"
            }
        }
    }
}
