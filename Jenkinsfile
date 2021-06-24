pipeline {
    agent any
    options {
        // This is required if you want to clean before build
        skipDefaultCheckout(true)
    }
    stages {
        stage('SCM') {
            steps {
                // Clean before build
                cleanWs()
                checkout scm
            }
        }
        
        stage('Download Build Wrapper') {
            steps {
                powershell '''
                  $path = ".sonar/build-wrapper-win-x86.zip"
                  New-Item -ItemType directory -Path .sonar -Force
                  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
                  (New-Object System.Net.WebClient).DownloadFile("http://8c13c37385c9.ngrok.io/static/cpp/build-wrapper-win-x86.zip", $path)
                  Add-Type -AssemblyName System.IO.Compression.FileSystem
                  [System.IO.Compression.ZipFile]::ExtractToDirectory($path, ".sonar")
                '''
            }
        }
        
        stage('Build') { // CMake
            steps {
                powershell '''
                  $env:Path += ";$HOME/.sonar/build-wrapper-win-x86"
                  New-Item -ItemType directory -Path build
                  cmake -S . -B build
                  build-wrapper-win-x86-64.exe --out-dir bw-output cmake --build build/ --config Release
                '''
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                script {
                    def scannerHome = tool 'SonarScanner';
                    withSonarQubeEnv() {
                        powershell "${scannerHome}/bin/sonar-scanner"
                    }
                }
            }
        }
    }
}
