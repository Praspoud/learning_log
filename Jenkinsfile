pipeline {
    
    agent any 
    
    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
        DOCKERHUB_CREDENTIALS = credentials('praspoud-dockerhub')
    }
    
    stages {
        
        stage('Checkout'){
           steps {
                git branch: 'devops', url: 'https://github.com/Praspoud/learning_log.git'
           }
        }

        stage('Build Docker'){
            steps{
                script{
                    sh '''
                    echo 'Buid Docker Image'
                    docker build -t praspoud/learninglog:${BUILD_NUMBER} .
                    '''
                }
            }
        }

        stage('Push the artifacts'){
           steps{
                script{
                    sh '''
                    echo 'Push to Repo'
                    echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                    docker push praspoud/learninglog:${BUILD_NUMBER}
                    docker logout
                    '''
                }
            }
        }
        
        // stage('Checkout K8S manifest SCM'){
        //     steps {
        //         git branch: 'devops', url: 'https://github.com/Praspoud/learning_log.git'
        //     }
        // }
        
        stage('Update K8S manifest & push to Repo'){
            steps {
                script{
                    withCredentials([usernamePassword(credentialsId: 'praspoud-github', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                        sh '''
                        cat deploy.yaml
                        sed -i '' "s/32/${BUILD_NUMBER}/g" deploy.yaml
                        cat deploy.yaml
                        git add deploy.yaml
                        git commit -m 'Updated the deploy yaml | Jenkins Pipeline'
                        git remote -v
                        git push https://github.com/Praspoud/learning_log.git HEAD:devops
                        '''                        
                    }
                }
            }
        }
    }
}