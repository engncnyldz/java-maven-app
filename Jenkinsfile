pipeline {
    agent any
    environment {
                ANSIBLE_MASTER_NODE_IP = credentials('ansible_master_node_ip')
    }
    stages {
        stage("copy files to ansible server") {
            
            steps {
                script {
                    echo "copying all necessary files to ansible control node"
                    sshagent(['ansible-server-key']) {
                        sh "scp -o StrictHostKeyChecking=no ansible/* ubuntu@${ANSIBLE_MASTER_NODE_IP}:/home/ubuntu"

                        withCredentials([sshUserPrivateKey(credentialsId: "ec2-server-key", keyFileVariable: 'keyfile', usernameVariable: 'user')]) {
                            sh 'scp $keyfile ubuntu@$ANSIBLE_MASTER_NODE_IP:/home/ubuntu/ssh-key.pem'
                        }
                    }
                }
            }
        }
        stage("execute ansible playbook") {
            steps {
                script {
                    echo "calling ansible playbook to configure ec2 instances"
                    def remote = [:]
                    remote.name = "ansible-server"
                    remote.host = env.ANSIBLE_MASTER_NODE_IP
                    remote.allowAnyHosts = true
                    withCredentials([sshUserPrivateKey(credentialsId: "ansible-server-key", keyFileVariable: 'keyfile', usernameVariable: 'user')]) {
                        remote.user = user
                        remote.identityFile = keyfile
                        sshCommand remote: remote, command: "ls -l"
                    }
                    
                }
            }
        }
    }
}