pipeline {
    agent any

    tools {
        maven 'Maven3'
        jdk 'JDK17'
    }

    stage('Checkout') {
    steps {
        checkout([
            $class: 'GitSCM',
            branches: [[name: '*/feature/devops-setup']],
            userRemoteConfigs: [[
                url: 'git@github.com:SamuelFregene/NumberGuessGame.git',
                credentialsId: 'github-ssh-key'
            ]]
        ])
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
                deploy adapters: [tomcat9(credentialsId: 'tomcat-creds',
                                          path: '',
                                          url: 'http://localhost:8081')],
                       contextPath: 'NumberGuessGame',
                       war: '**/target/*.war'
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: '**/target/*.war', followSymlinks: false
        }
    }



