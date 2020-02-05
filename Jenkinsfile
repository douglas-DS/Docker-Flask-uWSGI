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

    stage('Build image')
        def customImage = docker.build("${imageName}")
        slackNotifier(currentBuild.currentResult, 'Build')

    stage('Push image')
    try {
        customImage.push()
        customImage.push('latest')
    } catch(err) {
        if (err) slackNotifier(currentBuild.currentResult, 'Push')
    }
    slackNotifier(currentBuild.currentResult, 'Push')

    stage('Delivery')
        sh "kubectl apply -f k8s_app.yaml"
        sh "kubectl set image deployments/${appName} ${appName}=${imageName}"
        sh "kubectl rollout status deployments/${appName}"
        slackNotifier(currentBuild.currentResult, 'Deploy PROD')
}
