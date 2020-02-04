node {
    checkout scm
    sh "git rev-parse --short HEAD > commit-id"
    tag = readFile('commit-id').replace("\n", "").replace("\r", "")
    companyName="douglasso"
    appName = "app"
    imageName = "${companyName}/${appName}:${tag}"

    stage('Build')
        def customImage = docker.build("${imageName}")
        slackNotifier(currentBuild.currentResult)

    stage('Push')
        customImage.push()
        slackNotifier(currentBuild.currentResult)

    stage('Deploy PROD')
        customImage.push('latest')
        sh "kubectl apply -f k8s_app.yaml"
        sh "kubectl set image deployments/${appName} ${appName}=${imageName}"
        sh "kubectl rollout status deployments/${appName}"
        slackNotifier(currentBuild.currentResult)
}
