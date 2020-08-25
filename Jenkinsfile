def CONTAINER_NAME="app-pipeline-332488"
def CONTAINER_TAG="latest"
def DOCKER_HUB_USER="debjyotidas"
def HTTP_PORT="8090"


node {

        def app_name = 'app-pipeline-332488'
        def app_dockerfile_name = 'Dockerfile'
        def app_container_name = 'app-pipeline-332488'
        def app_tag="latest"


    stage('Initialize'){
        def dockerHome = tool 'myDocker'
        def mavenHome  = tool 'myMaven'
        env.PATH = "${dockerHome}/bin:${mavenHome}/bin:${env.PATH}"
    }

    stage('Checkout') {
        checkout scm
    }

    stage('Build'){
        sh "mvn clean install"
    }

    //stage('Sonar'){
    //    try {
    //        sh "mvn sonar:sonar -Dsonar.host.url=http://52.143.7.186/debjyoti/sonarqube -Dsonar.login=c8554f519b55dd83d85968887427498f3d30daca"
    //    } catch(error){
    //        echo "The sonar server could not be reached ${error}"
    //    }
    // }


    stage('Build and Push to Docker Registry'){
                withCredentials([usernamePassword(credentialsId: 'dockerHubAccount', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                        sh ("docker login -u ${USERNAME} -p ${PASSWORD}")
                        sh ("docker build -t ${USERNAME}/${app_name}:${BUILD_NUMBER} --pull --no-cache .")
                        sh ("docker push ${USERNAME}/${app_name}:${BUILD_NUMBER}")
                }
                echo "Image push complete"
    }

    stage('Deploy Application on K8s') {
                sh("curl -LO https://storage.googleapis.com/kubernetes-release/release/\$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl")
                sh("chmod +x ./kubectl")
                sh("cat ./${app_name}.yaml | sed s/1.0.0/${BUILD_NUMBER}/g | ./kubectl apply -f -")
                echo "Application started on port: HTTP_PORT (http)"
    }

}
