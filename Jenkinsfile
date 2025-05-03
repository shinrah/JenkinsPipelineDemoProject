pipeline{
    agent any
    environment {
      "PATH = /opt/apache-maven-3.9.9/bin/:$PATH"
    }
    stages{
        stage ('hello'){
            steps{
                echo "hello world"
            }
        }
        stage ('Checkout Git Branch'){
            steps {
            git url: 'https://github.com/shinrah/JenkinsPipelineDemoProject.git', branch: 'DEV'
            }
        }
        stage ('build'){
            steps{
               sh 'mvn clean install' // this run mvn command to build
            }
        }
    }
}
