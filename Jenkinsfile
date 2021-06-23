pipeline {
    agent any
    stages {
        stage('SCM') {
            git 'https://github.com/foo/bar.git'
        }
        stage('Download Build Wrapper') {
            steps {
                sh "mkdir -p .sonar"
                sh "curl -sSLo build-wrapper-linux-x86.zip ${baseUrl}/static/cpp/build-wrapper-linux-x86.zip"
                sh "unzip -o build-wrapper-linux-x86.zip -d .sonar"
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
