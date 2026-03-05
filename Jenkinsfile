pipeline {
    agent any

    stages {

        stage('Sonar Analysis') {
            steps {
                echo "LMS code analysis"
                sh '''
                cd webapp
                docker run --rm \
                -e SONAR_HOST_URL="http://54.196.218.167:9000" \
                -e SONAR_TOKEN="sqp_fe2ca190a3fc1d77ec6ab910fe599b0077bd8e55" \
                -v "$PWD:/usr/src" \
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
                    def packageJson = readJSON file: 'webapp/package.json'
                    def version = packageJson.version

                    sh """
                    cd webapp
                    zip -r lms-${version}.zip dist/*

                    curl -v -u admin:nexus12345 \
                    --upload-file lms-${version}.zip \
                    http://54.196.218.167:8081/repository/lms/lms-${version}.zip
                    """
                }
            }
        }

        stage('Deploy LMS') {
            steps {
                script {
                    def packageJson = readJSON file: 'webapp/package.json'
                    def version = packageJson.version

                    sh """
                    curl -u admin:nexus12345 -O \
                    http://54.196.218.167:8081/repository/lms/lms-${version}.zip

                    sudo rm -rf /var/www/html/*
                    sudo unzip -o lms-${version}.zip -d /var/www/html/
                    """
                }
            }
        }
    }
}    