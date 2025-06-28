pipeline {
    agent any

    environment {
        TARGET_HOST = '65.0.86.190'
        DEPLOY_USER = 'ubuntu'
        REMOTE_HTML_PATH = '/var/www/html'
    }

    stages {
        stage('Clone Code') {
            steps {
                git url: 'https://github.com/ascmis6/aws-devops.git', branch: 'main'
            }
        }

        stage('Build') {
            steps {
                echo 'Simulating build step...'
                sh '''
                    mkdir -p build
                    for item in *; do
                        [ "$item" != "build" ] && cp -r "$item" build/
                    done
                    echo "<!-- Build timestamp: $(date) -->" >> build/index.html
                '''
            }
        }

        stage('Test') {
            steps {
                echo 'Running basic HTML check...'
                sh '''
                    if grep -iq "<html" build/index.html; then
                        echo "HTML tag found, test passed."
                    else
                        echo "ERROR: index.html missing <html> tag!"
                        exit 1
                    fi
                '''
            }
        }

        stage('Package') {
            steps {
                echo 'Packaging HTML for deployment...'
                sh '''
                    tar -czf html_package.tar.gz -C build .
                '''
            }
        }

        stage('Deploy to Web Server') {
            steps {
                sshagent(credentials: ['webserver-ssh']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no ${DEPLOY_USER}@${TARGET_HOST} 'sudo mkdir -p ${REMOTE_HTML_PATH} /tmp/html'
                        scp -o StrictHostKeyChecking=no html_package.tar.gz ${DEPLOY_USER}@${TARGET_HOST}:/tmp/html/
                        ssh -o StrictHostKeyChecking=no ${DEPLOY_USER}@${TARGET_HOST} '
                            tar -xzf /tmp/html/html_package.tar.gz -C /tmp/html &&
                            sudo cp -r /tmp/html/* ${REMOTE_HTML_PATH}/ &&
                            sudo systemctl restart apache2
                        '
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '🎉 Deployment successful!'
        }
        failure {
            echo '❌ Deployment failed!'
        }
    }
}
