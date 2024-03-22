pipeline {
environment { // Declaration of environment variables
    DOCKER_ID = "fouad45" // replace this with your docker-id
    DOCKER_IMAGE_CAST = "cast-service"
    DOCKER_IMAGE_MOVIE = "movie-service"
    DOCKER_TAG = "v.${BUILD_ID}.0" // we will tag our images with the current build in order to increment the value by 1 with each new build
}
agent any // Jenkins will be able to select all available agents
stages {
        stage(' Docker Build'){ // docker build image stage
            steps {
                script {
                sh '''
                 docker rm -f jenkins
                 docker build -t $DOCKER_ID/$DOCKER_IMAGE_CAST:$DOCKER_TAG .
                 docker build -t $DOCKER_ID/$DOCKER_IMAGE_MOVIE:$DOCKER_TAG .
                sleep 6
                '''
                }
            }
        }
        stage('Test Acceptance'){ // we launch the curl command to validate that the container responds to the request
            steps {
                    script {
                    sh '''
                    curl localhost
                    '''
                    }
            }

        }
        stage('Docker Push'){ //we pass the built image to our docker hub account
            environment
            {
                DOCKER_PASS = credentials("DOCKER_HUB_PASS") // we retrieve  docker password from secret text called docker_hub_pass saved on jenkins
            }

            steps {

                script {
                sh '''
                docker login -u $DOCKER_ID -p $DOCKER_PASS
                docker push $DOCKER_ID/$DOCKER_IMAGE_CAST:$DOCKER_TAG
                docker push $DOCKER_ID/$DOCKER_IMAGE_MOVIE:$DOCKER_TAG
                '''
                }
            }

        }

stage('Deploiement en dev'){
        environment
        {
        KUBECONFIG = credentials("config") // we retrieve  kubeconfig from secret file called config saved on jenkins
        }
            steps {
                script {
                sh '''
                rm -Rf .kube
                mkdir .kube
                ls
                cat $KUBECONFIG > .kube/config
                cp manifests/db/values/values-dev.yaml values.yml
                sed -i "s+tag.*+tag: ${DOCKER_TAG}+g" values.yml
                cat values.yml
                helm upgrade --install manifests/db/ cast-db-dev --values=values.yml --namespace dev
                
                cp manifests/db/values/values-dev.yaml values.yml
                sed -i "s+tag.*+tag: ${DOCKER_TAG}+g" values.yml
                cat values.yml
                helm upgrade --install manifests/db/ movie-db-dev --values=values.yml --namespace dev
                '''
                }
                script {
                sh '''
                rm -Rf .kube
                mkdir .kube
                ls
                cat $KUBECONFIG > .kube/config
                cp manifests/service/values/values-dev.yaml values.yml
                sed -i "s+tag.*+tag: ${DOCKER_TAG}+g" values.yml
                cat values.yml
                helm upgrade --install manifests/service/ cast-service-dev --values=values.yml --namespace dev
                
                cp manifests/service/values/values-dev.yaml values.yml
                sed -i "s+tag.*+tag: ${DOCKER_TAG}+g" values.yml
                cat values.yml
                helm upgrade --install manifests/service/ movie-service-dev --values=values.yml --namespace dev
                '''
                }
            }

        }
stage('Deploiement en staging'){
        stage('Deploiement en staging'){
        environment
        {
        KUBECONFIG = credentials("config") // we retrieve  kubeconfig from secret file called config saved on jenkins
        }
            steps {
                script {
                sh '''
                rm -Rf .kube
                mkdir .kube
                ls
                cat $KUBECONFIG > .kube/config
                cp manifests/db/values/values-staging.yaml values.yml
                sed -i "s+tag.*+tag: ${DOCKER_TAG}+g" values.yml
                cat values.yml
                helm upgrade --install manifests/db/ cast-db-staging --values=values.yml --namespace staging
                
                cp manifests/db/values/values-staging.yaml values.yml
                sed -i "s+tag.*+tag: ${DOCKER_TAG}+g" values.yml
                cat values.yml
                helm upgrade --install manifests/db/ movie-db-staging --values=values.yml --namespace staging
                '''
                }
                script {
                sh '''
                rm -Rf .kube
                mkdir .kube
                ls
                cat $KUBECONFIG > .kube/config
                cp manifests/service/values/values-staging.yaml values.yml
                sed -i "s+tag.*+tag: ${DOCKER_TAG}+g" values.yml
                cat values.yml
                helm upgrade --install manifests/service/ cast-service-staging --values=values.yml --namespace staging
                
                cp manifests/service/values/values-staging.yaml values.yml
                sed -i "s+tag.*+tag: ${DOCKER_TAG}+g" values.yml
                cat values.yml
                helm upgrade --install manifests/service/ movie-service-staging --values=values.yml --namespace staging
                '''
                }
            }

        }

        }
stage('Deploiement en QA'){
        environment
        {
        KUBECONFIG = credentials("config") // we retrieve  kubeconfig from secret file called config saved on jenkins
        }
            steps {
                script {
                sh '''
                rm -Rf .kube
                mkdir .kube
                ls
                cat $KUBECONFIG > .kube/config
                cp manifests/db/values/values-qa.yaml values.yml
                sed -i "s+tag.*+tag: ${DOCKER_TAG}+g" values.yml
                cat values.yml
                helm upgrade --install manifests/db/ cast-db-qa --values=values.yml --namespace qa
                
                cp manifests/db/values/values-qa.yaml values.yml
                sed -i "s+tag.*+tag: ${DOCKER_TAG}+g" values.yml
                cat values.yml
                helm upgrade --install manifests/db/ movie-db-qa --values=values.yml --namespace qa
                '''
                }
                script {
                sh '''
                rm -Rf .kube
                mkdir .kube
                ls
                cat $KUBECONFIG > .kube/config
                cp manifests/service/values/values-qa.yaml values.yml
                sed -i "s+tag.*+tag: ${DOCKER_TAG}+g" values.yml
                cat values.yml
                helm upgrade --install manifests/service/ cast-service-qa --values=values.yml --namespace qa
                
                cp manifests/service/values/values-qa.yaml values.yml
                sed -i "s+tag.*+tag: ${DOCKER_TAG}+g" values.yml
                cat values.yml
                helm upgrade --install manifests/service/ movie-service-qa --values=values.yml --namespace qa
                '''
                }
            }
        }
  stage('Deploiement en prod'){
        stage('Deploiement en prod'){
        environment
        {
        KUBECONFIG = credentials("config") // we retrieve  kubeconfig from secret file called config saved on jenkins
        }
            steps {
                
                script {
                sh '''
                rm -Rf .kube
                mkdir .kube
                ls
                cat $KUBECONFIG > .kube/config
                cp manifests/db/values/values-prod.yaml values.yml
                sed -i "s+tag.*+tag: ${DOCKER_TAG}+g" values.yml
                cat values.yml
                helm upgrade --install manifests/db/ cast-db-prod --values=values.yml --namespace prod
                
                cp manifests/db/values/values-prod.yaml values.yml
                sed -i "s+tag.*+tag: ${DOCKER_TAG}+g" values.yml
                cat values.yml
                helm upgrade --install manifests/db/ movie-db-prod --values=values.yml --namespace prod
                '''
                }

                script {
                sh '''
                rm -Rf .kube
                mkdir .kube
                ls
                cat $KUBECONFIG > .kube/config
                cp manifests/service/values/values-prod.yaml values.yml
                sed -i "s+tag.*+tag: ${DOCKER_TAG}+g" values.yml
                cat values.yml
                helm upgrade --install manifests/service/ cast-service-prod --values=values.yml --namespace prod
                
                cp manifests/service/values/values-prod.yaml values.yml
                sed -i "s+tag.*+tag: ${DOCKER_TAG}+g" values.yml
                cat values.yml
                helm upgrade --install manifests/service/ movie-service-prod --values=values.yml --namespace prod
                '''
                }
            }

        }

        }

}
}
