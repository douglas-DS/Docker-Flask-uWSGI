@Library('jenkins-shared-library')_
pipeline {
    agent any
    environment {
        companyName="douglasso"
        appName = "app"
    }
    stages {
        
        stage('Checkout') {
            steps {
                checkout scm
                sh "git rev-parse --short HEAD > commit-id"
            }
            tag = readFile('commit-id').replace("\n", "").replace("\r", "")
            imageName = "${companyName}/${appName}:${tag}"
        }

        stage('Build') {
            customImage = docker.build("${imageName}")
            steps {
                customImage.push()
            }
        }

        stage('Deploy PROD') {
            steps {
                customImage.push('latest')
                sh "kubectl apply -f k8s_app.yaml"
                sh "kubectl set image deployments/${appName} ${appName}=${imageName}"
                sh "kubectl rollout status deployments/${appName}"
            }
        }
    }
    
    post {
        always {
            slackNotifier(currentBuild.currentResult)
        }
    }
}
