@Library('jenkins-shared-library')_
pipeline {
    agent any
    checkout scm
    sh "git rev-parse --short HEAD > commit-id"
    tag = readFile('commit-id').replace("\n", "").replace("\r", "")
    companyName="douglasso"
    appName = "app"
    imageName = "${companyName}/${appName}:${tag}"

    stages {
        stage('Build') {
            steps {
                def customImage = docker.build("${imageName}")
            }
        }

        stage('Push') {
            steps {
                customImage.push()
            }
        }

        stage('Deploy PROD') {
            steps {
                customImage.push('latest')
                sh "kubectl apply -f https://raw.githubusercontent.com/douglas-DS/Docker-Flask-uWSGI/master/k8s_app.yaml"
                sh "kubectl set image deployments/app app=${imageName}"
                sh "kubectl rollout status deployments/app"
            }
        }
    }
    
    post {
        always {
            slackNotifier(currentBuild.currentResult)
        }
    }
}
