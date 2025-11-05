pipeline {
    agent any

<<<<<<< HEAD
=======
    tools {
        nodejs "Nodejs"
    }

    stages {

        stage("Checkout code") {
            steps {
                git branch: 'main',
                    url: 'https://github.com/jemimahrimdan/React-ToDoList.git'
            }
        }

        stage("Build image and push") {
            steps {
                withCredentials([
                    usernamePassword(credentialsId: 'docker_creds', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')
                ]) {

                    sh '''
                        docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD
                        docker build -t $DOCKER_USERNAME/ci_backend_full_pipeline:v1 -f backend/Dockerfile backend
                        docker build -t $DOCKER_USERNAME/ci_frontend_full_pipeline:v1 -f dive-react-app/Dockerfile dive-react-app
                        docker push $DOCKER_USERNAME/ci_backend_full_pipeline:v1
                        docker push $DOCKER_USERNAME/ci_frontend_full_pipeline:v1
                    '''
                }
            }
        }

        stage("Deploy to EC2") {
            steps {
                withCredentials([
                    sshUserPrivateKey(credentialsId: 'EC2_KEY', keyFileVariable: 'SSH_KEY'),
                    string(credentialsId: 'EC2_HOST', variable: 'EC2_HOST'),
                    usernamePassword(credentialsId: 'docker_creds', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')
                ]) {

                    sh '''
    chmod 600 $SSH_KEY

    ssh -o StrictHostKeyChecking=no -i $SSH_KEY ubuntu@$EC2_HOST << EOF
        echo "Connected to EC2"
        export DOCKER_USERNAME="$DOCKER_USERNAME"
        export DOCKER_PASSWORD="$DOCKER_PASSWORD"
        cd /home/ubuntu/React-ToDoList
        bash ~/React-ToDoList/deploy.sh
EOF
'''

                }
            }
        }

    }

    post {
        always {
            cleanWs()
        }
        success {
            emailext(
                subject: "SUCCESS: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                body: "The build ${env.BUILD_URL} completed successfully.",
                to: "jemimahrimdan0@gmail.com"
            )
        }
        failure {
            emailext(
                subject: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                body: "The build ${env.BUILD_URL} failed. Please check the logs.",
                to: "jemimahrimdan0@gmail.com"
            )
        }
    }
}

