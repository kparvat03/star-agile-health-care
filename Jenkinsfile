pipeline{
    agent any
    stages{
        stage('Git Checkout'){
            steps{
                 git branch: 'master' url: 'https://github.com/kparvat03/star-agile-health-care.git'
                 echo 'This step is to clone repo from github'
                 
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
                
            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: '/var/lib/jenkins/workspace/Healthcare/target/surefire-reports', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: '', useWrapperFileDirectly: true])
            echo 'This stage will generate test reports using TestNG'
            }
            
        }
      
    }
}
