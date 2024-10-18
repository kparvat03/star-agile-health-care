pipeline{
    agent any
      tools {
          maven 'M2_HOME'
            }
    
    stages{
        stage('Git Checkout'){
            steps{
                 git url: 'https://github.com/kparvat03/star-agile-health-care/'
                 echo 'This stage is to clone repo from github'
                 
            }
        }
        stage('Create Package'){
            steps{
                sh 'mvn package'
                echo 'This stage will compile, test, package the application'
            }
        }
        stage('Generate Test Reports'){
            steps{
            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: '/var/lib/jenkins/workspace/Healthcare', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: '', useWrapperFileDirectly: true])
            echo 'This stage will generate test reports using TestNG'
            }
            
        }
        stage('Create Docker Image'){
            steps{
                sh 'docker build -t kparvat03/healthcare:1.0 .'
                echo 'This stage will create docker image'
            }
        }

       stage('Login to Docker'){
            steps{
                withCredentials([usernamePassword(credentialsId: 'dockerloginnew', passwordVariable: 'dockerpass', usernameVariable: 'dockeruser')]) {
                sh 'docker login -u ${dockeruser} -p ${dockerpass}'
                echo 'This stage will login to Dockerhub'
                   }
                }
       }
        stage("Docker Push-Image"){
            steps {
                sh 'docker push kparvat03/healthcare:1.0'
                echo 'This stage will pus the new image to the dockerhub'
            }
        }

        stage('AWS-Login') {
      steps {
        withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'Awsaccess', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
         }
      }
    }
   stage('Terraform Operations for test workspace') {
            steps {
                script {
                    sh '''
                        terraform workspace select test || terraform workspace new test
                        terraform init
                        terraform plan
                        terraform apply -auto-approve   # Apply changes instead of destroy
                    '''
                }
            }
        }
        
        stage('Get kubeconfig for test') {
            steps {
                sh 'aws eks wait cluster-active --region us-east-1 --name test-cluster'   # Wait until the cluster is ready
                sh 'aws eks update-kubeconfig --region us-east-1 --name test-cluster'
                sh 'kubectl get nodes'
            }
        }
        
        stage('Deploy application to test') {
            steps {
                sh 'kubectl apply -f app-deploy.yml'
                sh 'kubectl get svc'
            }
        }

        stage('Terraform Operations for Production workspace') {
            when {
                expression {
                    return currentBuild.currentResult == 'SUCCESS'
                }
            }
            steps {
                script {
                    sh '''
                        terraform workspace select prod || terraform workspace new prod
                        terraform init
                        terraform plan
                        terraform apply -auto-approve   # Apply changes instead of destroy
                    '''
                }
            }
        }

        stage('Get kubeconfig for production') {
            steps {
                sh 'aws eks wait cluster-active --region us-east-1 --name prod-cluster'   # Wait until the cluster is ready
                sh 'aws eks update-kubeconfig --region us-east-1 --name prod-cluster'
                sh 'kubectl get nodes'
            }
        }

        stage('Deploy application to production') {
            steps {
                sh 'kubectl apply -f app-deploy.yml'
                sh 'kubectl get svc'
            }
        }
    }

    post {
        always {
            script {
                // Optional cleanup, like terraform destroy if necessary
                sh '''
                    terraform workspace select test
                    terraform destroy -auto-approve
                    terraform workspace select prod
                    terraform destroy -auto-approve
                '''
            }
        }
    }
      
    
}
