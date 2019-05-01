def CONTAINER_NAME="jenkins-pipeline"
def CONTAINER_TAG="${env.tag}"
def DOCKER_HUB_USER="iflan7744"
def HTTP_PORT="8090"

node {

    stage('Initialize'){
        def dockerHome = tool 'leadIQ-Docker'
        def mavenHome  = tool 'leadIQ-Maven'
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
        withCredentials([usernamePassword(credentialsId: 'docker_hub_account', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
            pushToImage(CONTAINER_NAME, CONTAINER_TAG, USERNAME, PASSWORD)
        }
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
    sh "docker tag $containerName:$CONTAINER_TAG $dockerUser/$containerName:latest"
    sh "docker push $dockerUser/$containerName:latest"
    echo "Image push complete"
}

