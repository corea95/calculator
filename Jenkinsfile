pipeline {
    agent any
    environment {
	registry = "dusqja231/calculator"
    registryCredential = "dockerhub"
 	dockerImage = ''
    }
    stages {
        stage ('Check out'){
            steps {
               git url: 'https://github.com/corea95/calculator.git'
            }
        }
        stage ('Compile') {
            steps {
                sh './gradlew compileJava'
            }
        }
        stage ('Unit Test') {
           steps {
                sh "./gradlew test"
            }
        }
        stage ('Code Coverage'){
            steps {
                sh "./gradlew jacocoTestReport"
                publishHTML ( target: [
                    reportDir: 'build/reports/jacoco/test/html',
                    reportFiles: 'index.html',
                    reportName: "JaCoCo Report"
                ])
                sh "./gradlew jacocoTestCoverageVerification"
            }
        }
        stage ('Static Code Analysis'){
            steps {
                sh "./gradlew checkstyleMain"
                publishHTML (target: [
                    reportDir: 'build/reports/checkstyle/',
                    reportFiles: 'main.html',
                    reportName: "Checkstyle Report"
                ])
            }
        }
        stage ('Packaging'){
            steps {
                sh "./gradlew build"
            }
        }
        stage ('Deploy Image') {
            steps {
		script {
		    dockerImage = docker.build registry + ":$BUILD_NUMBER"
		}
            }
        }
	stage('Push Image') {
	    steps {
		script {
		    docker.withRegistry('',registryCredential ) {
		        dockerImage.push()
                dockerImage.push("latest")
		    }
		}
	  }
    }
  }
}
