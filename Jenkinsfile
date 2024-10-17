pipeline{
    agent any
    stages{
        stage('Git Checkout'){
            steps{
                 echo 'This step is to clone repo from github'
                 git branch: 'master' url: 'https://github.com/kparvat03/star-agile-health-care.git'
                 
            }
        }
        stage('Create Package'){
            steps{
                echo 'This stage will compile, test, package the application'
                sh 'mvn package'
            }
        }
        stage('Generate Test Reports'){
            steps{
                echo 'This stage will generate test reports using TestNG'
                publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: '/var/lib/jenkins/workspace/Healthcare/target/surefire-reports', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: '', useWrapperFileDirectly: true])
            }
        }
      
    }
}
