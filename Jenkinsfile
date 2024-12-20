pipeline {
    agent any
    
    stages {

        stage('Build') {
            steps {
                script {
                    // Update and upgrade system packages
                    sh 'apt-get update'
                    sh 'apt-get upgrade -y'

                    // Install Docker if it's not already installed
                    sh '''
                        if ! command -v docker &> /dev/null
                        then
                            echo "Docker not found, installing..."
                            apt-get install -y apt-transport-https ca-certificates curl software-properties-common
                            curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
                            add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
                            apt-get update
                            apt-get install -y docker-ce docker-ce-cli containerd.io
                        else
                            echo "Docker is already installed"
                        fi
                    '''
                    
                    // Check Docker Compose version
                    sh 'docker-compose version'
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    // Update and upgrade system packages again
                    sh 'apt-get update'
                    sh 'apt-get upgrade -y'

                    // Install Docker Compose if it's not already installed
                    sh '''
                        if ! command -v docker-compose &> /dev/null
                        then
                            echo "Docker Compose not found, installing..."
                            curl -L https://github.com/docker/compose/releases/download/v2.32.1/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
                            chmod +x /usr/local/bin/docker-compose
                        else
                            echo "Docker Compose is already installed"
                        fi
                    '''
                    
                    // Check Docker Compose version and run the container
                    sh '''
                        docker-compose version
                        docker-compose up -d
                    '''

                    // Install Python dependencies
                    sh 'apt-get install -y python3 python3-venv python3-pip'

                    // Set up virtual environment and run tests
                    sh '''
                        python3 -m venv .venv
                        . .venv/bin/activate
                        pip install pytest selenium

                        sleep 15
                        python test_devopstest.py
                    '''
                }
            }
        }
    }
}
