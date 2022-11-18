pipeline{
    //agent any
    agent{
        label "javaAgent"
         }

    tools{
         maven 'maven:3.6.3'
          }

       environment {
            DEPLOY = "${env.BRANCH_NAME == "python-dramed" || env.BRANCH_NAME == "master" ? "true" : "false"}"
            NAME = "${env.BRANCH_NAME == "python-dramed" ? "example" : "example-staging"}"
            def mavenHome =  tool name: "maven:3.6.3", type: "maven"
            //def mavenCMD = "${mavenHome}/usr/share/maven"
            VERSION = "${env.BUILD_ID}"
            REGISTRY = 'eagunuworld/mongodb-springboot-app'
            REGISTRY_CREDENTIAL = 'eagunuworld_dockerhub_creds'
          }

    stages {


      // stage('Example') {
      //      if (env.BRANCH_NAME == 'python-dramed') {
      //            echo 'I only execute on this environment $env.BRANCH_NAME'
      //          } else {
      //               echo 'I execute elsewhere'
      //              }
      //           }
      stage('checking... maven version ') {
          steps {
                  sh "mvn --version"
                   }
                }

      // stage(" Maven buld Artifacts Package"){
      //             def mavenCMD = "${mavenHome}/bin/mvn"
      //             sh "${mavenCMD} clean package"
      //           }

     stage('Build maven packages '){
              steps{
                    sh "mvn clean package"
                      }
                  }

      stage('Building Docker Images') {
                steps {
                  sh "sudo chmod 666 /var/run/docker.sock"
                  sh "docker build -t ${REGISTRY}:${VERSION} ."
                     }
                 }

        stage('Push Docker Image To DockerHub') {
              steps {
                   withCredentials([string(credentialsId: 'eagunuworld_dockerhub_creds', variable: 'eagunuworld_dockerhub_creds')])  {
                   sh "docker login -u eagunuworld -p ${eagunuworld_dockerhub_creds} "
                   }
                 sh 'docker push ${REGISTRY}:${VERSION}'
                }
             }
    stage('Display All Files In Console') {
            steps {
               sh 'ls -lart'
            }
          }

    stage('Docker Run') {
        steps{
            script {
              sh 'docker run -d -p 8080:8080 --rm --name framed ${REGISTRY}:${VERSION}'
               }
             }
         }
    stage('Deleting Old versions ') {
        agent {
              label "python-node"
                  }
            steps {
                sh "docker rmi -f ${REGISTRY}:${VERSION}"
              }
        }
   }
}
