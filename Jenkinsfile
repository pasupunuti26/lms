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

        stage('Release LMS') {
            steps {
                script {
                    echo "Publish LMS Artifacts"

                    def packageJSON = readJSON file: 'webapp/package.json'
                    def version = packageJSON.version

                    sh """
                        cd webapp
                        zip -r dist-${version}.zip dist
                        curl -v -u admin:nexus12345 \
                        --upload-file dist-${version}.zip \
                        http://35.172.194.59:8081/repository/lms/dist-${version}.zip
                    """
                }
            }
        }

        stage('Deploy LMS') {
            steps {
                script {
                    echo "Deploy LMS"

                    def packageJSON = readJSON file: 'webapp/package.json'
                    def version = packageJSON.version

                    sh """
                        curl -u admin:nexus12345 -O \
                        http://35.172.194.59:8081/repository/lms/dist-${version}.zip

                        rm -rf /var/www/html/*
                        unzip -o dist-${version}.zip -d /var/www/html
                    """
                }
            }
        }

    }
