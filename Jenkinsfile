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

        stage('SonarQube analysis') {
        environment {
           scannerHome = tool 'valaxy-sonar-scanner'
    }    
            steps{
            withSonarQubeEnv('valaxy-sonarqube-server') {
              sh "${scannerHome}/bin/sonar-scanner"   

    }
     
    }    
    }    
    }


}

