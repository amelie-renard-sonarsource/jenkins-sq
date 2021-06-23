pipeline {
    agent any
    stages {
        stage('SCM') {
            steps {
                git 'https://github.com/amelie-renard-sonarsource/jenkins-sq'
            }
        }
        stage('Download Build Wrapper') {
            steps {
                bat 'mkdir -p .sonar'
                bat 'curl -sSLo build-wrapper-win-x86.zip ${baseUrl}/static/cpp/build-wrapper-win-x86.zip'
            }
        }
        stage('Build') {
            steps {
                sh '.sonar/build-wrapper-win-x86/build-wrapper-win-x86-64 --out-dir bw-output make clean all'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                script {
                    def scannerHome = tool 'SonarScanner';
                    withSonarQubeEnv('Jenkins-SQ') {
                        sh "\${scannerHome}/bin/sonar-scanner -Dsonar.cfamily.build-wrapper-output=bw-output"
                    }
                }
            }
        }
    }
}
