pipeline {
    agent any
    stages {
        stage('Sonar Analysis') {
            steps {
                echo 'LMS code analysis'
                sh """
                    cd webapp
                    sudo docker run --rm \
                    -e SONAR_HOST_URL="http://34.229.128.122:9000" \
                    -e SONAR_TOKEN="sqp_fe2ca190a3fc1d77ec6ab910fe599b0077bd8e55" \
                    -v "\$(pwd)":/usr/src \
                    sonarsource/sonar-scanner-cli \
                    -Dsonar.projectKey=lms \
                    -Dsonar.projectName="LMS Project" \
                    -Dsonar.sources=.
                """
            }
        }
        stages {
            stage('Build LMS') {
                steps {
                    echo 'LMS Build'
                    sh """
                        cd webapp
                        npm install
                        npm run build
                    """
                }
            }
        }
    }

    
        
    
            