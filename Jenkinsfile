pipeline {
    agent {
        node {
            label 'maven'
        }
    }

    stages {
        stage('build'){
            steps {
                echo "----------- unit test started -----------"
                sh 'mvn clean deploy -Dmaven.test.skip=true'
                echo "----------- unit test ended -------------"
            }
        }

        stage("test"){
            steps{
                echo "----------- unit test started -----------"
                sh 'mvn surefire-report:report'
                echo "----------- unit test ended -------------"
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

