def notifyBuild(String buildStatus, String committerEmail) {
    // build status of null means successful
    buildStatus =  buildStatus ?: 'SUCCESSFUL'

    // Default values
    def color = 'RED'
    def colorCode = '#FF0000'
    def subject = "${buildStatus}: Job -- ${env.JOB_NAME} [${env.BUILD_NUMBER}]"

    def summary = "${subject} (${env.BUILD_URL}console)"
    def details = """<p>Check console output at "<a href="${env.BUILD_URL}console">${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>"</p>
                  <p>Check the Test Reports at -- <a href="${env.BUILD_URL}testReport">Test Reports</a></p>
                  <p>Check the Code Coverage Reports at -- <a href="${env.BUILD_URL}coverage">jacoco Code Coverage Reports</a></p>
                  
                  <br />
                  <p>From,<br />Jenkins</p>"""
if (buildStatus == 'SUCCESSFUL') {
        emailext(
            to: "${committerEmail}",
            //to: '$DEFAULT_RECIPIENTS',
            subject: subject,
            body: details,
            //attachmentsPattern: attachmentsPattern,
            //recipientProviders: [[$class: 'DevelopersRecipientProvider']]
            mimeType: "text/html"
        )
    }
}
pipeline {
    agent any 
//    {
//       docker {
//           image 'maven:3.8.1-adoptopenjdk-11'
//           args '-v /root/.m2:/root/.m2'
//       }
//    }
    tools{
            maven 'maven'
     }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build') {
            steps {
                script {
                    def committerEmail = sh (script: 'git --no-pager show -s --format=\'%ae\'', returnStdout: true).trim()
                    try {
                        sh 'mvn -B -DskipTests clean package'
                        notifyBuild('SUCCESSFUL', committerEmail)
                    }
                    catch (exc) {
                        notifyBuild('FAILED_BUILD', committerEmail)
                    throw(exc)
                    }
                }
            }
        }
        stage('Test') { 
            steps {
                sh 'mvn test' 
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml' 
                }
            }
        }
        stage('Deliver') { 
            steps {
                sh './jenkins/scripts/deliver.sh' 
            }
        }
    }
}
    
