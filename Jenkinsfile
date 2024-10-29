pipeline {
    agent { node { label 'agent' } } 
    stages {
        stage("Build") {
            when {
                beforeAgent true
                anyOf {
                    branch "master"
                    buildingTag()
                }
            }
            steps {
                script {
                    checkout scm
                    IMAGE_TAG=BUILD_ID
                    if(env.TAG_NAME){
                        IMAGE_TAG = TAG_NAME
                    }
                    env.IMAGE_TAG =  IMAGE_TAG
                    def image = docker.build("$JOB_BASE_NAME:$IMAGE_TAG","-f pipeline/Dockerfile .")
                }
            }
        }
        stage("Deploy") {
            steps {
                script {
                    if(env.TAG_NAME) {
                        env.TARGET_ENVIRONMENT = input message: "Which version do you want to deploy ?",
                        parameters:[
                            choice(name: 'TARGET_ENVIRONMENT', choices : ["staging","production"])
                        ]
                    } else {
                        env.TARGET_ENVIRONMENT = "development"
                    }
                    checkout scm
                    sh """
                    echo "deploy to: $TARGET_ENVIRONMENT with tag ${env.IMAGE_TAG}"
                    """
                }
            }
        }
    }
}
