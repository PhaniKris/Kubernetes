pipeline {
    agent any

    environment {
        // Define your environment variables here
        ARTIFACTORY_URL = 'https://your.artifactory.url'
        ARTIFACTORY_REPO = 'your-repo-name'
        SONARQUBE_URL = 'https://your.sonarqube.server'
        SONARQUBE_TOKEN = credentials('sonarqube-token') // Assume you have a Jenkins credential set up
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    // Run SonarQube analysis
                    withSonarQubeEnv('SonarQube') { // 'SonarQube' is the name of your SonarQube server configuration in Jenkins
                        sh 'mvn sonar:sonar -Dsonar.projectKey=your-project-key'
                    }
                }
            }
        }

        stage('Build') {
            steps {
                // Build the application, e.g., using Maven
                sh 'mvn clean package'
            }
        }

        stage('Staging to Artifactory') {
            steps {
                script {
                    // Upload the artifact to Artifactory
                    def server = Artifactory.server 'Artifactory' // 'Artifactory' is the ID of your Artifactory server configuration in Jenkins
                    def uploadSpec = """{
                        "files": [
                            {
                                "pattern": "target/*.jar", // Adjust this pattern according to your artifact
                                "target": "${ARTIFACTORY_REPO}/"
                            }
                        ]
                    }"""
                    server.upload(uploadSpec)
                }
            }
        }

        stage('Deploy') {
            steps {
                // Deploy the application (this could vary based on your environment)
                sh './deploy.sh' // Adjust this command based on your deployment process
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}

