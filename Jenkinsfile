pipeline {
    agent any

    environment {
        GITHUB_REPO = 'Keyz078/jenkins-ci-cd'
        GITHUB_BRANCH = 'main'
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout repositori dari GitHub
                git branch: "${env.GITHUB_BRANCH}", url: "https://github.com/${env.GITHUB_REPO}.git"
            }
        }

        stage('Load Variables') {
            steps {
                script {
                    // Load variabel dari file values.yaml
                    def values = readYaml file: 'values.yaml'
                    env.DEPLOYMENT_NAME = values.DEPLOYMENT_NAME
                    env.APP_LABEL = values.APP_LABEL
                    env.REPLICA_COUNT = values.REPLICA_COUNT
                    env.CONTAINER_NAME = values.CONTAINER_NAME
                    env.IMAGE = values.IMAGE
                    env.CONTAINER_PORT = values.CONTAINER_PORT.toString()
                    env.APP_ENV = values.APP_ENV
                    env.PVC_NAME = values.PVC_NAME
                    env.SERVICE_NAME = values.SERVICE_NAME
                    env.PVC_STORAGE = values.PVC_STORAGE
                }
            }
        }

        stage('Create Deployment YAML') {
            steps {
                script {
                    // Baca template dan ganti dengan variabel
                    def deploymentTemplate = readFile 'deployment-template.yaml'
                    deploymentTemplate = deploymentTemplate.replace('${DEPLOYMENT_NAME}', env.DEPLOYMENT_NAME)
                    deploymentTemplate = deploymentTemplate.replace('${APP_LABEL}', env.APP_LABEL)
                    deploymentTemplate = deploymentTemplate.replace('${REPLICA_COUNT}', env.REPLICA_COUNT.toString())
                    deploymentTemplate = deploymentTemplate.replace('${CONTAINER_NAME}', env.CONTAINER_NAME)
                    deploymentTemplate = deploymentTemplate.replace('${IMAGE}', env.IMAGE)
                    deploymentTemplate = deploymentTemplate.replace('${CONTAINER_PORT}', env.CONTAINER_PORT)
                    deploymentTemplate = deploymentTemplate.replace('${APP_ENV}', env.APP_ENV)
                    deploymentTemplate = deploymentTemplate.replace('${PVC_NAME}', env.PVC_NAME)

                    // Simpan hasil YAML ke file baru
                    writeFile file: 'php-deployment.yaml', text: deploymentTemplate
                }
            }
        }

        stage('Create Service YAML') {
            steps {
                script {
                    // Baca template service dan ganti dengan variabel
                    def serviceTemplate = readFile 'service-template.yaml'
                    serviceTemplate = serviceTemplate.replace('${SERVICE_NAME}', env.SERVICE_NAME)
                    serviceTemplate = serviceTemplate.replace('${APP_LABEL}', env.APP_LABEL)
                    
                    // Simpan hasil YAML ke file baru
                    writeFile file: 'php-service.yaml', text: serviceTemplate
                }
            }
        }

        stage('Create PVC YAML') {
            steps {
                script {
                    // Baca template PVC dan ganti dengan variabel
                    def pvcTemplate = readFile 'pvc-template.yaml'
                    pvcTemplate = pvcTemplate.replace('${PVC_NAME}', env.PVC_NAME)
                    pvcTemplate = pvcTemplate.replace('${PVC_STORAGE}', env.PVC_STORAGE)
                    
                    // Simpan hasil YAML ke file baru
                    writeFile file: 'php-pvc.yaml', text: pvcTemplate
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                // Deploy ke Kubernetes
                sh 'kubectl apply -f php-deployment.yaml'
                sh 'kubectl apply -f php-service.yaml'
                sh 'kubectl apply -f php-pvc.yaml'
            }
        }
    }
}