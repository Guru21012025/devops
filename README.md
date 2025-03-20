# devops
pipeline {
    agent any

    environment {
        WORKSPACE_DIR = '/var/lib/jenkins/workspace'
    }

    stages {
        stage('Git to pull the file') {
            steps {
                // Pull the repository
                git branch: 'main', url: 'https://github.com/Guru21012025/devops.git'
            }
        }

        stage('Check Podman Installation') {
            steps {
                script {
                    // Check if Podman is installed
                    sh '''
                        if ! command -v podman &> /dev/null; then
                            echo "Podman not found, exiting pipeline."
                            exit 1
                        fi
                    '''
                }
            }
        }

        stage('Build Podman Image') {
            steps {
                script {
                    // Build the Podman image
                    sh '''
                        set -e
                        sudo podman build -t sample1 .
                    '''
                }
            }
        }

        stage('Run the Podman Container on Port 8010') {
            steps {
                script {
                    // Ensure port 8010 is free by killing any process using it
                    sh '''
                        set -e
                        if ! command -v lsof &> /dev/null; then
                            echo "lsof is not installed, skipping port check."
                        else
                            pid=$(sudo lsof -t -i :8010)
                            if [ ! -z "$pid" ]; then 
                                sudo kill -9 $pid
                                echo "Killed process using port 8010"
                            else
                                echo "No process found on port 8010"
                            fi
                        fi
                    '''
                    
                    // Run the container with mounted index.html
                    sh '''
                        sudo podman run -d -p 8010:80 sample1
                    '''
                    
                    // Verify the content is served
                   // sh 'curl http://localhost:8010/index.html'
                }
            }
        }
    }
}

#sudo mkdir /home/nielilt/Desktop/dir1
sudo git pull 'https://github.com/Guru21012025/devops.git' 
sudo docker build -t my_image .
sudo docker run -d -p 800{$Buid NUMBER}:80 my_image
