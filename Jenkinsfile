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
                bat 'curl --create-dirs -sSLo .sonar\build-wrapper-win-x86.zip ${baseUrl}/static/cpp/build-wrapper-win-x86.zip'
                bat 'unzip -o .sonar\build-wrapper-win-x86.zip -d .sonar\'
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
