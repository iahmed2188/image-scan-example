pipeline {
    
    agent any
    
    environment {
        // Replace the <tags> below with your values.
        FALCON_CLIENT_SECRET = credentials('FALCON_CLIENT_SECRET')
        FALCON_CLIENT_ID = credentials('FALCON_CLIENT_ID')
        BUILD_DIR = '.'
        CONTAINER_REPO = 'iahmed2188/image-scan-example'
        CONTAINER_TAG = "${BUILD_NUMBER}"
        FALCON_CLOUD_REGION = 'us-1'
        SCORE = 4000
    }
    
    stages {
        stage('BuildImage') {
            // Build the docker image using the docker file in the current directory
            steps{
                sh "docker build -t $CONTAINER_REPO:$CONTAINER_TAG $BUILD_DIR"
            }
        }
        stage('ScanImage') {
            // Scan the image using Falcon ImageScan API
            steps{
                sh '''
                if [ ! -d container-image-scan ] ; then
                    git clone https://github.com/crowdstrike/container-image-scan
                fi
                pip3 install docker-py
                python3 container-image-scan/cs_scanimage.py
                '''
            }
        }  
        stage('PushImage') {
            // Push the container image
            steps{
                withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId:'DockerHub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]) {
                sh '''
                docker login -u $USERNAME -p $PASSWORD
                docker push $CONTAINER_REPO:$CONTAINER_TAG
                '''
                }
            }
        }  
    }
}
