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
                        set PATH=C:\\Users\\Kyle\\sonar-scanner\\sonar-scanner-4.8.0.2856-windows\\bin;%PATH%
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
