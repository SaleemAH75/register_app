pipeline{
    agent {label 'Jenkins-Agent'}

    tools{
        jdk "Java17"
        maven "Maven3"
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

        stage("Quality Gate"){
                timeout(time: 1, unit: 'MINUTES') { // Just in case something goes wrong, pipeline will be killed after a timeout
                def qg = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
                if (qg.status != 'OK') {
                error "Pipeline aborted due to quality gate failure: ${qg.status}"
                    }
                }
        }
    }
}