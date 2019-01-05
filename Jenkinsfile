def CONTAINER_NAME="jenkins-pipeline"
def CONTAINER_TAG="${env.BUILD_NUMBER}"
def DOCKER_HUB_USER="iflan7744"
def HTTP_PORT="8090"

node {

    stage('Initialize'){
        def dockerHome = tool 'myDocker'
        def mavenHome  = tool 'myMaven'
        env.PATH = "${dockerHome}/bin:${mavenHome}/bin:${env.PATH}"
    }

    stage('Checkout') {
        checkout scm
    }



    stage("Image Prune"){
        imagePrune(CONTAINER_NAME)
    }

    stage('Image Build'){
        imageBuild(CONTAINER_NAME, CONTAINER_TAG)
    }

    stage('Push to Docker Registry'){
        withCredentials([usernamePassword(credentialsId: 'dockerHubAccount', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
            pushToImage(CONTAINER_NAME, CONTAINER_TAG, USERNAME, PASSWORD)
        }
    }

    stage('Run App'){
        runApp(CONTAINER_NAME, CONTAINER_TAG, DOCKER_HUB_USER, HTTP_PORT)
    }



}

def imagePrune(containerName){
    try {
         sh "docker image prune -f"
        sh "docker stop $containerName"
    } catch(error){}
}

def imageBuild(containerName, CONTAINER_TAG){
    sh "docker build -t $containerName:$CONTAINER_TAG  -t $containerName --pull --no-cache ."
    echo "Image build complete"
}

def pushToImage(containerName, CONTAINER_TAG, dockerUser, dockerPassword){
    sh "docker login -u $dockerUser -p $dockerPassword"
    sh "docker tag $containerName:$CONTAINER_TAG $dockerUser/$containerName:$CONTAINER_TAG"
    sh "docker push $dockerUser/$containerName:$CONTAINER_TAG"
    echo "Image push complete"
}

def runApp(containerName, CONTAINER_TAG, dockerHubUser, httpPort){
    sh "docker pull $dockerHubUser/$containerName"
    sh "sudo helm upgrade test11 /root/mychart/ --set image.tag=$CONTAINER_TAG"
    sh "helm ls"
    echo "Application started on port: ${httpPort} (http)"
}
