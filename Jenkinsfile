pipeline {
    agent any
    stages {
        stage("copy files to ansible server") {
            environment {
                ANSIBLE_MASTER_NODE_IP = credentials('ansible_master_node_ip')
            }
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
    }
}