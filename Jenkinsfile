#! /usr/bin/env groovy

pipeline {

  agent {
    label 'maven'
  }

  stages {
    stage('Build') {
      steps {
        echo 'Building..'
        
        sh 'mvn clean package'
      }
    }
    stage('Create Container Image') {
      steps {
        echo 'Create Container Image..'
        
        script {

          
          openshift.withCluster() { 
              openshift.withProject("sahuopen-dev") {
  
                    def buildConfigExists = openshift.selector("bc", "codelikethewind_1").exists() 
    
                    if(!buildConfigExists){ 
                      openshift.newBuild("--name=codelikethewind_1", "--docker-image=registry.redhat.io/jboss-eap-7/eap74-openjdk8-openshift-rhel7", "--binary") 
//                       openshift.newBuild("--name=codelikethewind_1", "--docker-image= redhat-openjdk18-openshift", "--binary") 
                     
                     } 
    
                    openshift.selector("bc", "codelikethewind_1").startBuild("--from-file=target/simple-servlet-0.0.1-SNAPSHOT.war", "--follow") } }

          }
      }
    }
    stage('Deploy') {
      steps {
        echo 'Deploying....'
        script {

          
          openshift.withCluster() { 
              openshift.withProject("sahuopen-dev") { 
                             def deployment = openshift.selector("dc", "codelikethewind_1") 

                            if(!deployment.exists()){ 
                              openshift.newApp('codelikethewind_1', "--as-deployment-config").narrow('svc').expose() 
                            } 

                            timeout(5) { 
                              openshift.selector("dc", "codelikethewind_1").related('pods').untilEach(1) { 
                                return (it.object().status.phase == "Running") 
      } 
    } 
  } 
}

        }
      }
    }
  }
}
