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
                sh 'docker build -t priyanka/Healthcare:1.0 .'
                echo 'This stage will create docker image'
            }
        }
      
    }
}
