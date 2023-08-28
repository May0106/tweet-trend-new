pipeline {
    agent {
        node {
            label 'maven'
        }
    }

    stages {
        stage('build'){
            steps {
                sh 'mvn clean deploy'
            }
        }

    stage('SonarQube analysis'){
    enviornment {
        scannerHome = tool 'sonar-scanner'
    }    
    steps{
    withSonarQubeEnv('sonarqube-server') {
       sh "${scannerHome}/bin/sonar-scannr"   

    }
     
    }    
    }    
    }


}

