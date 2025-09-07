pipeline {
    agent any

    environment {
        // Set JDK17 and Maven from Jenkins global tools
        JAVA_HOME = tool name: 'JDK17', type: 'jdk'
        MAVEN_HOME = tool name: 'Maven3', type: 'maven'
        PATH = "${JAVA_HOME}/bin:${MAVEN_HOME}/bin:${env.PATH}"

        // Tomcat deployment settings
        TOMCAT_URL = "http://3.17.71.244:8081/manager/text"
        TOMCAT_USER = "tomcat"
        TOMCAT_PASS = "s3cret"
        CONTEXT_PATH = "/NumberGuessGame"
        WAR_FILE = "target/NumberGuessGame-1.0-SNAPSHOT.war"
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

        stage('Deploy to Tomcat') {
            steps {
                sh """
                    echo "Deploying WAR to Tomcat..."
                    curl --upload-file ${WAR_FILE} \
                        --user ${TOMCAT_USER}:${TOMCAT_PASS} \
                        "${TOMCAT_URL}/deploy?path=${CONTEXT_PATH}&update=true"
                """
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
            echo 'Pipeline failed! Check logs for details.'
        }
    }
}
