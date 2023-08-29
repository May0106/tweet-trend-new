def registry = 'https://may0106.jfrog.io'
pipeline {
    agent {
        node {
            label 'maven'
        }
    }

    stages {
        stage('build'){
            steps {
                echo "-----------unit test started-----------"
                sh 'mvn clean deploy -Dmaven.test.skip=true'
                echo "-----------unit test ended-------------"
            }
        }

        stage("test"){
            steps{
                echo "-----------unit test started-----------"
                sh 'mvn surefire-report:report'
                echo "-----------unit test ended-------------"
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

    stage("Quality gate"){
        steps {
            script {
                timeout(time: 1, unit: 'HOURS') {
                    def qg = waitForQualityGate()
                    if (qg.status != 'OK') {
                    error "pipeline borted due to quality gate failure: ${qg.status}"
                    }
                }
            }
        }
    }

    stage("Jar Publish") {
        steps {
            script {
                    echo '<--------------- Jar Publish Started --------------->'
                     def server = Artifactory.newServer url:registry+"/artifactory" ,  credentialsId:"artifact-cred"
                     def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}";
                     def uploadSpec = """{
                          "files": [
                            {
                              "pattern": "jarstaging/(*)",
                              "target": "libs-release-local/{1}",
                              "flat": "false",
                              "props" : "${properties}",
                              "exclusions": [ "*.sha1", "*.md5"]
                            }
                         ]
                     }"""
                     def buildInfo = server.upload(uploadSpec)
                     buildInfo.env.collect()
                     server.publishBuildInfo(buildInfo)
                     echo '<--------------- Jar Publish Ended --------------->' 
            }
            }   
        } 
    }


}

