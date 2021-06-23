pipeline {
    agent any
    stages {
        stage('SCM') {
            steps {
                checkout scm
            }
        }
        
        stage('Download Build Wrapper') {
            steps {
                powershell '''
                  $path = ".sonar/build-wrapper-win-x86.zip"
                  rm build-wrapper-win-x86 -Recurse -Force -ErrorAction SilentlyContinue
                  rm $path -Force -ErrorAction SilentlyContinue
                  mkdir .sonar
                  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
                  (New-Object System.Net.WebClient).DownloadFile("http://6d497a4d65db.ngrok.io/static/cpp/build-wrapper-win-x86.zip", $path)
                  Add-Type -AssemblyName System.IO.Compression.FileSystem
                  [System.IO.Compression.ZipFile]::ExtractToDirectory($path, ".sonar")
                '''
            }
        }
        
/*        stage('Build') { // CMake
            steps {
                powershell '''
                  $env:Path += ";$HOME/.sonar/build-wrapper-win-x86"
                  New-Item -ItemType directory -Path build
                  cmake -S . -B build
                  build-wrapper-win-x86-64.exe --out-dir bw-output cmake --build build/ --config Release
                '''
            }
        }*/
        
        stage('Build') { // MsBuild
            steps {
                powershell '''
                  $env:Path += ";$HOME/.sonar/build-wrapper-win-x86"
                  $path = vswhere -latest -requires Microsoft.Component.MSBuild -find MSBuild\**\Bin\MSBuild.exe | select-object -first 1
                  build-wrapper-win-x86-64.exe --out-dir build_wrapper_output_directory $path sonar_scanner_example.vcxproj /t:rebuild
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
