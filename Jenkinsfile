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
            terraform destroy -auto-approve
          '''
        }
      }
    }
    stage('Terraform destroy & apply for test workspace') {
      steps {
        sh 'terraform apply -auto-approve'
      }
    }
    stage('get kubeconfig') {
      steps {
        sh 'aws eks update-kubeconfig --region us-east-1 --name test-cluster'
        sh 'kubectl get nodes'
      }
    }
    stage('Deploying the application') {
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
            terraform destroy -auto-approve
          '''
        }
      }
    }
    stage('Terraform destroy & apply for production workspace') {
      steps {
        sh 'terraform apply -auto-approve'
      }
    }
    stage('get kubeconfig for production') {
      steps {
        sh 'aws eks update-kubeconfig --region us-east-1 --name prod-cluster'
        sh 'kubectl get nodes'
      }
    }
    stage('Deploying the application to production') {
      steps {
        sh 'kubectl apply -f app-deploy.yml'
        sh 'kubectl get svc'
      }
    }
      
    }
}
