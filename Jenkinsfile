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
                script {
                    curl -sSLo build-wrapper-linux-x86.zip http://6d497a4d65db.ngrok.io/static/cpp/build-wrapper-linux-x86.zip
                    unzip -o build-wrapper-linux-x86.zip -d .sonar
                }
            }
        }
        stage('Build') {
            steps {
                sh ".sonar/build-wrapper-linux-x86/build-wrapper-linux-x86-64 --out-dir bw-output make clean all"
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
