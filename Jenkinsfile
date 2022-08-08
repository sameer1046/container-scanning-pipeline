
pipeline {
    agent any
    parameters {
        booleanParam(
            name: 'CHECKOUT',
            defaultValue: true,
            description: 'Checkout TERN'
        )
        booleanParam(
            name: 'BUILD',
            defaultValue: true,
            description: 'Build TERN'
        )
        booleanParam(
            name: 'DOCKER_BUILD',
            defaultValue: true,
            description: 'Build Docker Image'
        )
        booleanParam(
            name: 'DOCKER_SCAN',
            defaultValue: true,
            description: 'Scan Docker Image'
        ) 
        string(
            name: 'DOCKER_IMAGE',
            description: 'Docker Image to Scan',
            defaultValue: 'ternd:latest'
        )
    }
    stages {
        stage('Checkout') {
            when {
                    expression {
                    params.CHECKOUT
                }
            }
            steps {
                echo 'Checkout TERN'
                git branch: 'main', url: 'https://github.com/tern-tools/tern.git'
                
            }
        }
        stage('Build') {
            when {
                    expression {
                    params.BUILD
                }
            }
            steps {
                echo 'Build TERN'  
                sh 'python3 -m pip install --upgrade build  virtualenv'
                sh 'python3 -m build'
            }
        }
        stage('Docker Build') {
            when {
                    expression {
                    params.DOCKER_BUILD
                }
            }
            steps {
                echo 'Build TERN Docker Images'  
                sh 'mkdir -p ./ci/dist/ && cp ./dist/*.tar.gz ./ci/dist/tern-*.tar.gz'
                sh 'docker build -t ternd ./ci/'
            }
        }
        stage('Docker Scan') {
            when {
                    expression {
                    params.DOCKER_SCAN
                }
            }
            steps {
                echo 'Scan Docker Image'  
                sh 'mkdir -p ./output'
                sh './docker_run.sh ternd "report -i $DOCKER_IMAGE" > output/$DOCKER_IMAGE.txt'
            }
        }        

    }
        post {
        success {
            archiveArtifacts artifacts: 'output/*.txt', onlyIfSuccessful: true
        }
    }
}
