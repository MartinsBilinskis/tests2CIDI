// --- PIEGĀDES KONVEIJERA (PIPELINE) DEFINĪCIJA ---

pipeline {
    agent any // Izpilda uz jebkura pieejamā Jenkins aģenta

    stages {
        stage('install-pip-deps') {
            steps {
                script {
                    installDependencies()
                }
            }
        }
        
        stage('deploy-to-dev') {
            steps {
                script {
                    deploy('dev', '7001')
                }
            }
        }
        stage('tests-on-dev') {
            steps {
                script {
                    runTests('DEV')
                }
            }
        }

        stage('deploy-to-stg') {
            steps {
                script {
                    deploy('stg', '7002')
                }
            }
        }
        stage('tests-on-stg') {
            steps {
                script {
                    runTests('STG')
                }
            }
        }

        stage('deploy-to-preprod') {
            steps {
                script {
                    deploy('preprod', '7003')
                }
            }
        }
        stage('tests-on-preprod') {
            steps {
                script {
                    runTests('PREPROD')
                }
            }
        }

        // --- PROD VIDE ---
        stage('deploy-to-prod') {
            steps {
                script {
                    deploy('PROD', '7004')
                }
            }
        }
        stage('tests-on-prod') {
            steps {
                script {
                    runTests('prod')
                }
            }
        }
    }
}

// --- FUNKCIJU DEFINĪCIJAS ---

def installDependencies() {
    echo "Installing all required dependencies"

    git branch: 'main', url: 'https://github.com/mtararujs/python-greetings'
    
    pwsh 'ls'
    
    pwsh 'python -m venv venv'
    pwsh 'venv\\Scripts\\Activate.ps1'
    pwsh 'venv\\Scripts\\python.exe -m pip install -r requirements.txt'
}

def deploy(String envName, String port) {
    echo "Deploying application to ${envName} environment on port ${port}..."
    git branch: 'main', url: 'https://github.com/mtararujs/python-greetings'
    
    pwsh '& "pm2" delete "greetings-app-${envName}"; if ($LASTEXITCODE -ne 0) { exit 0 }'
    
    pwsh "pm2 start 'venv\\Scripts\\python.exe' --name 'greetings-app-${envName}' '--' app.py --port ${port}"
}

def runTests(String envName) {
    echo "Starting tests for ${envName} environment..."
    dir('api-tests') {
        git branch: 'main', url: 'https://github.com/mtararujs/course-js-api-framework'
        
        pwsh 'npm install'
        
        pwsh "npm run greetings greetings_${envName}"
    }
}

