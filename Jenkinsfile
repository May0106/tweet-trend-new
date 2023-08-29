def registry = 'https://may0106.jfrog.io'
def imageName = 'valaxy01.jfrog.io/valaxy-docker-local/ttrend'
def version   = '2.1.2'
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
                     def server = Artifactory.newServer url:registry+"/artifactory" ,  credentialsId:"artfiact-cred"
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

        stage(" Docker Build ") {
      steps {
        script {
           echo '<--------------- Docker Build Started --------------->'
           app = docker.build(imageName+":"+version)
           echo '<--------------- Docker Build Ends --------------->'
        }
      }
    }
    stage (" Docker Publish "){
        steps {
            script {
               echo '<--------------- Docker Publish Started --------------->'  
                docker.withRegistry(registry, 'artifact-cred'){
                    app.push()
                }    
               echo '<--------------- Docker Publish Ended --------------->'  
            }
        }
    }  
    }



}

