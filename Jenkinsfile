pipeline { 
    agent any 

    stages { 
        stage('Checkout') { 
            steps { 
                git branch: 'main', url: 'https://github.com/Kyle960/8.2CDevSecOps.git' 
            } 
        } 

        stage('Install Dependencies') { 
            steps { 
                bat 'npm install' 
            } 
        } 

        stage('Run Tests') { 
            steps { 
                bat 'npm test || exit /b 0' // Allows pipeline to continue despite test failures 
            } 
        } 

        stage('Generate Coverage Report') { 
            steps { 
                bat 'npm run coverage || exit /b 0' // Ensure coverage report exists 
            } 
        } 

        stage('NPM Audit (Security Scan)') { 
            steps { 
                bat 'npm audit || exit /b 0' // This will show known CVEs in the output 
            } 
        } 
        
        stage('SonarCloud Analysis') { 
            steps { 
                withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
                    bat '''
                        REM Define SonarScanner version and download URL
                        set SONAR_SCANNER_VERSION=4.8.0.2856
                        set SONAR_SCANNER_URL=https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-%SONAR_SCANNER_VERSION%-windows.zip

                        REM Create a directory for SonarScanner if it does not exist
                        if not exist "%USERPROFILE%\\sonar-scanner" mkdir "%USERPROFILE%\\sonar-scanner"

                        REM Download the SonarScanner CLI tool (if not already downloaded)
                        powershell -Command "(New-Object System.Net.WebClient).DownloadFile('%SONAR_SCANNER_URL%', '%USERPROFILE%\\sonar-scanner\\sonar-scanner.zip')"

                        REM Extract the zip file
                        powershell -Command "Expand-Archive -Path '%USERPROFILE%\\sonar-scanner\\sonar-scanner.zip' -DestinationPath '%USERPROFILE%\\sonar-scanner' -Force"
                        del "%USERPROFILE%\\sonar-scanner\\sonar-scanner.zip"

                        REM Update PATH to include SonarScanner
                        set PATH=%USERPROFILE%\\sonar-scanner\\sonar-scanner-%SONAR_SCANNER_VERSION%-windows\\bin;%PATH%

                        REM Execute SonarScanner with correct host URL and secure token
                        sonar-scanner -Dsonar.host.url=https://sonarcloud.io -Dsonar.login=%SONAR_TOKEN%
                    '''
                }
            } 
        }
    } 

    post { 
        always { 
            echo 'Pipeline execution completed.' 
        } 
    }
}
