pipeline {
    agent any

    environment {
        PATH = "/opt/homebrew/bin:${env.PATH}"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Anshuman3311/8.2CDevSecOps.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'npm test || true'
            }
        }

        stage('Generate Coverage Report') {
            steps {
                sh 'npm run coverage || true'
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                sh 'npm audit || true'
            }
        }

        stage('SonarCloud Analysis') {
            steps {
                withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
                    sh '''
                        set -e

                        SCANNER_VERSION="8.0.1"
                        SCANNER_NAME="sonar-scanner-${SCANNER_VERSION}-macosx-aarch64"
                        SCANNER_ZIP="${SCANNER_NAME}.zip"
                        SCANNER_URL="https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/${SCANNER_ZIP}"

                        echo "Downloading SonarScanner CLI ${SCANNER_VERSION}..."
                        curl -fL -o "${WORKSPACE}/${SCANNER_ZIP}" "${SCANNER_URL}"

                        echo "Extracting SonarScanner..."
                        rm -rf "${WORKSPACE}/${SCANNER_NAME}"
                        unzip -q "${WORKSPACE}/${SCANNER_ZIP}" -d "${WORKSPACE}"

                        echo "Running SonarCloud analysis..."
                        "${WORKSPACE}/${SCANNER_NAME}/bin/sonar-scanner"
                    '''
                }
            }
        }
    }
}
