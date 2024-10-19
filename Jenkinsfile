pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = credentials('1bb40899-65b9-4fa9-811e-93c08ac39701') 
        ANSIBLE_PLAYBOOK = 'ansible-playbook.yaml' 
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Create .ssh directory') {
            steps {
                sh 'mkdir -p /var/lib/jenkins/.ssh'
            }
        }

        stage('Add EC2 Host to Known Hosts') {
            steps {
                sshagent(['ec2-server-key']) {
                    sh "ssh-keyscan -H 16.170.224.165 >> ~/.ssh/known_hosts"
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t abdallabahrawy/project-app .'
                }
            }
        }

        stage('Login to DockerHub') {
            steps {
                script {
                        withCredentials([usernamePassword(credentialsId: '1bb40899-65b9-4fa9-811e-93c08ac39701', usernameVariable: 'dockerhub_username', passwordVariable: 'dockerhub_password')]) {
                        echo 'Logged into DockerHub'
                    }
                }
            }
        }

        stage('Push Docker Image to DockerHub') {
            steps {
                script {
                    sh 'docker push abdallabahrawy/project-app:latest'
                }
            }
        }

        stage('Run Ansible Playbook') {
            steps {
                sshagent(['ec2-server-key']) {
                    script {
                        sh """
                        ansible-playbook -i inventory ${ANSIBLE_PLAYBOOK} --extra-vars "@vars.yaml"
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
