pipeline {
    agent any

    stages {
        stage('Clone Code') {
            steps {
                git url: 'https://github.com/ascmis6/aws-devops.git', branch: 'main'
            }
        }

        stage('Deploy to Web Server') {
            steps {
                sshagent(credentials: ['webserver-ssh']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@65.0.86.190 'sudo mkdir -p /var/www/html'
                    scp -o StrictHostKeyChecking=no -r * ubuntu@65.0.86.190:/tmp/html/
                    ssh -o StrictHostKeyChecking=no ubuntu@65.0.86.190 'sudo cp -r /tmp/html/* /var/www/html/ && sudo systemctl restart apache2'
                    '''
                }
            }
        }
    }
}
