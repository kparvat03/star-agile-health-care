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
                sh 'docker build -t priyanka/healthcare:1.0 .'
                echo 'This stage will create docker image'
            }
        }

       stage('Login to Docker'){
            steps{
                withCredentials([usernamePassword(credentialsId: 'Dockerlogin', passwordVariable: 'docker-pass', usernameVariable: 'docker-login')]) {
                sh 'docker login -u ${docker-login} -p ${docker-pass}'
                echo 'This stage will login to Dockerhub'
                   }
                }
       }
        stage("Docker Push-Image"){
            steps {
                sh 'docker push priyanka/healthcare:1.0'
                echo 'This stage will pus the new image to the dockerhub'
            }
        }

        
      
    }
}
