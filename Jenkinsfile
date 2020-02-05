@Library('jenkins-shared-library')_
node {
    companyName="douglasso"
    appName = "app"

    stage('Checkout')
        checkout scm
        sh "git rev-parse --short HEAD > commit-id"
        def tag = readFile('commit-id').replace("\n", "").replace("\r", "")
        def imageName = "${companyName}/${appName}:${tag}"
        slackNotifier(currentBuild.currentResult, 'Checkout')

    stage('Build')
        def customImage = docker.build("${imageName}")
        slackNotifier(currentBuild.currentResult, 'Build')

    stage('Push')
    try {
        
        customImage.push()
        slackNotifier(currentBuild.currentResult, 'Push')
    } catch(err) {
        slackNotifier(currentBuild.currentResult, 'Push')
    }

    stage('Deploy PROD')
        //customImage.push('latest')
        sh "kubectl apply -f k8s_app.yaml"
        sh "kubectl set image deployments/${appName} ${appName}=${imageName}"
        sh "kubectl rollout status deployments/${appName}"
        slackNotifier(currentBuild.currentResult, 'Deploy PROD')
}
