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

     stage("Build & Push Docker Image") {
    steps {
        script {
            def imageTag = "${RELEASE}-${env.BUILD_NUMBER}"
            def dockerImage = docker.build("${IMAGE_NAME}:${imageTag}")

            docker.withRegistry('', DOCKER_PASS) {
                dockerImage.push()
                dockerImage.push('latest')
                    }
                }
            }
        }

        stage("trivy scan"){
            steps{
                script{
                    sh ('docker run -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image saleem45/register:latest --no-progress --scanners vuln  --exit-code 0 --severity HIGH,CRITICAL --format table')
                }
            }
        }

        stage("cleanup artifacts"){
            steps{
                script{
                    sh "docker rmi ${IMAGE_NAME}:${IMAGE_NAME}"
                    sh "docker rmi ${IMAGE_NAME}:latest"
                }
            }
        }
    }
}