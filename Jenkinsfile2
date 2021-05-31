node('node1') {
      withDockerContainer(args: '-v /root/.m2:/root/.m2', image: 'maven:3-alpine') {
       stage('Build') {
	      checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/prateekjain87/simple-java-maven-app']]]) 
              sh 'mvn -B -DskipTests clean package'
           }
       stage('Test') {
               sh 'mvn test'
               junit 'target/surefire-reports/*.xml'
           }

       stage('Notify') {
               def mailRecipients = "akijain058@gmail.com"
               def jobName = currentBuild.fullDisplayName
               if(currentBuild.result == 'FAILURE') {
                  emailext body: '''${JELLY_SCRIPT"}
                  &nbsp;<h2><b> SONARQUBE ANALYSIS </b></h2><br>
                  &nbsp;<a href="https://www.google.com" >View SONARQUBE Analysis Reports from here.</a>
                  <br><br>''',
                  mimeType: 'text/html',
                  subject: "[PS-Jenkins] ${jobName} Failure",
                  to: "${mailRecipients}",
                  replyTo: "prateek.jain@perennialsys.com",
                  recipientProviders: [[$class: 'CulpritsRecipientProvider']]
               }
              else {
                  emailext body: '''${JELLY_SCRIPT"}
                  &nbsp;<h2><b> SONARQUBE ANALYSIS </b></h2><br>
                  &nbsp;<a href="http://35.154.132.156:8080" >View SONARQUBE Analysis Reports from here.</a>
                  <br><br>
                  &nbsp;<h2><b> Application URL </b></h2><br>
                  &nbsp;<a href="http://35.154.132.156:8080" >You can access the application from here.</a>
                  <br><br>''',
                  mimeType: 'text/html',
                  subject: "[PS-Jenkins] ${jobName} Success",
                  to: "${mailRecipients}",
                  replyTo: "prateek.jain@perennialsys.com",
                  recipientProviders: [[$class: 'CulpritsRecipientProvider']]
               }
       }
  }
}
