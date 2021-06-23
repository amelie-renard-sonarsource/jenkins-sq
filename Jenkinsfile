pipeline {
    agent any
    stages {
        stage('SCM') {
            steps {
                checkout scm
            }
        }
 /*       stage('Download Build Wrapper') {
            steps {
                bat 'curl --create-dirs -sSLo .sonar\\build-wrapper-win-x86.zip http://6d497a4d65db.ngrok.io/static/cpp/build-wrapper-win-x86.zip'
                bat 'tar -xf .sonar\\build-wrapper-win-x86.zip'
                bat 'dir /b /a-d'
                bat '.sonar\\build-wrapper-win-x86\\build-wrapper-win-x86-64.exe --out-dir bw-output make clean all'
            }
        }*/
        
        stage('Download Build Wrapper') {
            steps {
                powershell '''
                  $path = ".sonar/build-wrapper-win-x86.zip"
                  rm build-wrapper-win-x86 -Recurse -Force -ErrorAction SilentlyContinue
                  rm $path -Force -ErrorAction SilentlyContinue
                  mkdir .sonar
                  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
                  (New-Object System.Net.WebClient).DownloadFile("http://6d497a4d65db.ngrok.io/static/cpp/build-wrapper-win-x86.zip", $path)
                '''
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
