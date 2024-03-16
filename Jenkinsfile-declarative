pipeline {
    agent any // Runs on the Jenkins master or any available agent
    environment {
        // Define environment variables if needed
        REPO_LOCAL_PATH = "${WORKSPACE}/sampleadf"
        PACKAGE_JSON_FOLDER = "ci-cd/packages"
        ARM_TEMPLATE_FOLDER = "${env.REPO_LOCAL_PATH}/ArmTemplate"
        RESOURCE_GROUP_NAME = "Prakhar-RG"
        FACTORY_NAME = "ADF001"
        SUBSCRIPTION_ID = "123-456-789"
    }
    stages {
        stage('Prepare Environment') {
            steps {
                script {
                    // Checks if Node.js and npm are installed
                    if (sh(script: 'node --version', returnStatus: true) != 0) {
                        error "Node.js is not installed"
                    }
                    if (sh(script: 'npm --version', returnStatus: true) != 0) {
                        error "npm is not installed"
                    }
                }
                echo 'Environment prepared.'
            }
        }
        stage('Install Dependencies') {
            steps {
                dir("${env.PACKAGE_JSON_FOLDER}") {
                    sh 'npm install --verbose'
                }
            }
        }
        stage('Validate Data Factory Resources') {
            steps {
                dir("${env.PACKAGE_JSON_FOLDER}") {
                    sh "npm run build validate ${env.REPO_LOCAL_PATH}/ /subscriptions/${env.SUBSCRIPTION_ID}/resourceGroups/${env.RESOURCE_GROUP_NAME}/providers/Microsoft.DataFactory/factories/${env.FACTORY_NAME}"
                }
            }
        }
        stage('Generate ARM Template') {
            steps {
                dir("${env.PACKAGE_JSON_FOLDER}") {
                    // Uncomment the next line if you want to use the preview feature
                    // sh "npm run build-preview export ${env.REPO_LOCAL_PATH} /subscriptions/${env.SUBSCRIPTION_ID}/resourceGroups/${env.RESOURCE_GROUP_NAME}/providers/Microsoft.DataFactory/factories/${env.FACTORY_NAME} \"ArmTemplate\""
                    sh "npm run build export ${env.REPO_LOCAL_PATH}/ /subscriptions/${env.SUBSCRIPTION_ID}/resourceGroups/${env.RESOURCE_GROUP_NAME}/providers/Microsoft.DataFactory/factories/${env.FACTORY_NAME} \"ArmTemplate\""
                }
            }
        }
        stage('Publish ARM Template') {
            steps {
                archiveArtifacts artifacts: "${env.ARM_TEMPLATE_FOLDER}/**", onlyIfSuccessful: true
            }
        }
    }
    post {
        always {
            echo 'Cleaning up...'
            cleanWs()
        }
    }
}
