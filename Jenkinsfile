pipeline {
    agent {
        label 'prod-agent'
    }
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }

    }
    stage('Build Docker Image') {
        when {
            branch 'master'
            }
        steps {
            script {
                app = docker.build("jonhpe/train-app")
                app.inside {
                    sh 'echo $(curl localhost:5000)'
                }
            }
        }
    }
    stage('Push Docker Image') {
        when {
            branch 'master'
        }
        steps {
            script {
                docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {
                    app.push("${env.BUILD_NUMBER}")
                    app.push("latest")
                }
            }
        }
    }
}
