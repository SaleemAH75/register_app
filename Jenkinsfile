pipeline{
    agent {label 'Jenkins-Agent'}

    tools{
        jdk "Java17"
        maven "Maven3"
    }

    environment{
        APP_NAME = "register-app"
        RELEASE  = "1.0.0"
        DOCKER_USER = "saleem45"
        DOCKER_PASS = 'Dockerhub'
        IMAGE_NAME  = "${DOCKER_USER}"+"/"+"${APP_NAME}"
        IMAGE_TAG   = "${RELEASE}"+"/"+"${BUILD_NUMBER}"

    }

    stages{
        stage ("cleanup workspace"){
            steps{
                cleanWs()
            }
        }

        stage ("checkout from scm"){
            steps{
                git branch: 'main', url: 'https://github.com/SaleemAH75/register_app.git', credentialsId: 'github'
            }
        }

        stage ("Build Application"){
            steps{
               sh "mvn clean package"
            }
        }

        stage ("Test Application"){
            steps{
               sh "mvn test"
            }
        }

        stage ("sonarQube Analysis"){
            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'sonartoken'){
                        sh "mvn sonar:sonar"
                    }
                }
            }
        }

     stage("Quality Gate") {
       steps {
         script{
            waitForQualityGate abortPipeline: false, credentialsId: 'sonartoken'
                }
            }   
        }

     stage("Build & Push Docker Iamge") {
        steps{
          script{
            docker.withRegistry('', DOCKER_PASS)
            docker_image=docker.build "${IMAGE_NAME}"
          }
          docker.withRegistry('', DOCKER_PASS){
            docker_image.push "${IMAGE_TAG}"
            docker_image.push('latest')
          }
        }
      }
    }
}