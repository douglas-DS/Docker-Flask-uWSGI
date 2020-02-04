@Library('jenkins-shared-library')_
pipeline {
    agent any
    
    stages {
        environment {
            companyName="douglasso"
            appName = "app"
            imageName = ""
            customImage = null
        }
        stage('Checkout') {
            steps {
                checkout scm
                sh "git rev-parse --short HEAD > commit-id"
                tag = readFile('commit-id').replace("\n", "").replace("\r", "")
                imageName = "${companyName}/${appName}:${tag}"
            }
        }

        stage('Build') {
            steps {
                customImage = docker.build("${imageName}")
                customImage.push()
            }
        }

        stage('Deploy PROD') {
            steps {
                customImage.push('latest')
                sh "kubectl apply -f https://raw.githubusercontent.com/douglas-DS/Docker-Flask-uWSGI/master/k8s_app.yaml"
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
