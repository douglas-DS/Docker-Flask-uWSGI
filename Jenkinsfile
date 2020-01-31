node {
    
    checkout scm
    sh "git rev-parse --short HEAD > commit-id"
    tag = readFile('commit-id').replace("\n", "").replace("\r", "")
    companyName="douglasso"
    appName = "app"
    imageName = "${companyName}/${appName}:${tag}"

    stage("Build")
        def customImage = docker.build("${imageName}")
    
    //stage("Push")
        //jsonCredentials = sh "kubectl get secret regcred --output="jsonpath={.data.\.dockerconfigjson}" | base64 --decode"
        //customImage.push()
    
    stage("Deploy PROD")
        //customImage.push('latest')
        sh "kubectl apply -f https://raw.githubusercontent.com/douglas-DS/Docker-Flask-uWSGI/master/k8s_app.yaml"
        //sh "kubectl apply -f /app/Docker-Flask-uWSGI/k8s_app.yaml"
        sh "kubectl set image deployments/app app=${imageName}"
        sh "kubectl rollout status deployments/app"
}
