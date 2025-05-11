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

        stage (""){
            steps{
                
            }
        }
    }
}