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
                //bat 'curl --create-dirs -sSLo build-wrapper-win-x86.zip http://6d497a4d65db.ngrok.io/static/cpp/build-wrapper-win-x86.zip'
                //bat 'tar -xf build-wrapper-win-x86.zip'
                bat 'dir /b /a-d'
                //bat 'build-wrapper-win-x86\build-wrapper-win-x86-64.exe --out-dir bw-output make clean all'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                script {
                    def scannerHome = tool 'SonarScanner';
                    withSonarQubeEnv('Jenkins-SQ') {
                        bat '\${scannerHome}/bin/sonar-scanner -Dsonar.cfamily.build-wrapper-output=bw-output'
                    }
                }
            }
        }
    }
}
