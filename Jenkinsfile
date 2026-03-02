pipeline {
    agent any

    stages {

        stage('Sonar Analysis') {
            steps {
                echo "LMS code analysis"
                sh '''
                    cd webapp
                    docker run --rm \
                    -e SONAR_HOST_URL="http://35.172.194.59:9000" \
                    -e SONAR_TOKEN="your_sonar_token_here" \
                    -v "$(pwd):/usr/src" \
                    sonarsource/sonar-scanner-cli \
                    -Dsonar.projectKey=lms \
                    -Dsonar.projectName="LMS Project" \
                    -Dsonar.sources=.
                '''
            }
        }

        stage('Build LMS') {
            steps {
                echo "LMS Build"
                sh '''
                    cd webapp
                    npm install
                    npm run build
                '''
            }
        }
    }