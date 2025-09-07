pipeline {
    agent any

    environment {
        // Set JDK17 and Maven from Jenkins global tools
        JAVA_HOME = tool name: 'JDK17', type: 'jdk'
        MAVEN_HOME = tool name: 'Maven3', type: 'maven'
        PATH = "${JAVA_HOME}/bin:${MAVEN_HOME}/bin:${env.PATH}"
    }

    stages {
        stage('Checkout') {
            steps {
                git(
                    url: 'git@github.com:SamuelFregene/NumberGuessGame.git',
                    branch: 'feature/devops-setup',
                    credentialsId: 'github-ssh-key'
                )
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Deploy') {
            steps {
                // Ensure the "Deploy to Container Plugin" is installed
                deploy adapters: [tomcat9(
                                    credentialsId: 'tomcat-creds',
                                    url: 'http://localhost:8081/manager/text'
                                  )],
                       contextPath: '/NumberGuessGame',
                       war: 'target/*.war'
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'target/*.war', allowEmptyArchive: true
        }
        success {
            echo 'Build, Test, and Deploy completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
