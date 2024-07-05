pipeline {
    agent any
	
    environment {
        MVN = tool name: 'Maven', type: 'maven'
    }
    stages {
	stage('Install pre requirements') {
            steps {
                //  Checkout the code from the GitLab repository
                sh "sudo apt update"
		sh "sudo apt install git maven docker.io -y"
            }
	}
        stage('Fetch Files') {
            steps {
                //  Checkout the code from the GitLab repository
                git branch: 'master', credentialsId: 'github_repo', url: 'https://github.com/tamarshnirer/Simple-Java-Calculator.git'
            }
        }
        stage('SonarQube Analysis') {
            // sonarqube
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh "${MVN}/bin/mvn clean verify sonar:sonar -Dsonar.projectKey=Maven -Dsonar.projectName='Maven'"
                }
            }
        }
        stage('Build') {
            steps {
                sh "sudo docker build -t multi-stage ."
		sh "sudo docker save -o my_image.tar multi-stage" 
            }
        }
        stage('push artifacts to jfrog') {
            steps {
                sh "sudo chmod 666 ./my_image.tar"
                sh "jf rt u ./my_image.tar Maven/Calculator"
                sh "sudo rm -f rm ./my_image.tar"
            }
        }
    }
	post{
	    success {
		    sh "sudo docker image ls -q | head -n -2 | xargs sudo docker image rm -f"
		    emailext attachLog: true, body: 'Build successded', replyTo: 'tamarsh224@gmail.com', subject: 'Build status', to: 'tamarsh224@gmail.com'
      }
	    failure {
                    sh "sudo docker image ls -q | head -n -2 | xargs sudo docker image rm -f"
		    emailext attachLog: true, body: 'Build failed', replyTo: 'tamarsh224@gmail.com', subject: 'Build status', to: 'tamarsh224@gmail.com'
      }
    }
}
