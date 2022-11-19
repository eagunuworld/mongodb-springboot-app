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

  stage('Update Image Tage in Compose  ') {
      steps {
            sh "sed -i '/<\/BUILD_TAG >/ i\${VERSION} docker-compose.yml' "
            }
      }

  stage('Display docker-compose content ') {
        steps {
            sh 'cat docker-compose.yml'
            }
        }

  stage('How to remove image') {
        steps{
            script {
                sh 'docker rmi  $(docker images -q)'
                  }
              }
            }

  stage('Display containers ') {
        steps {
              sh 'docker ps -f name=framed -q | xargs --no-run-if-empty docker container stop'
              sh 'docker container ls -a -fname=framed  -q | xargs -r docker container rm'
              sh 'docker container ls '
                }
            }

   }
}
