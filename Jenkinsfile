pipeline
{
 agent any
 environment {
     jmeter="/opt/jmeter/bin"
 }
 tools
 {
      maven 'maven'
 }   

 options 
 {
  buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '4', daysToKeepStr: '', numToKeepStr: '4')
  timestamps()
}
stages {
     stage('Code checkout')  {
         steps {
            script
             {
                 //step([$class: 'WsCleanup']
                 checkout([$class: 'WsCleanup', $class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/poornima4824/java-jmeter.git']]])
            }
             
         }
     }
    stage('Build') {   
         steps {
             sh "mvn clean package"
         }
     }
    stage('Jmeter test') {
         steps {
               sh "/opt/jmeter/bin/jmeter.sh -Jjmeter.save.saveservice.output_format=xml -n -t webapp/src/main/webapp/jmeter/Testing.jmx -l MyRun1.jtl"
               step([$class: 'ArtifactArchiver', artifacts: '**/*.jtl'])
              //sh "/opt/jmeter/bin/jmeter -Jjmeter.save.saveservice.output_format=xml -n -t src/main/jmeter/Testing.jmx -l src/main/jmeter/JMeter.jtl -e -o src/main/jmeter/report/output"
              //sh "/opt/jmeter/bin/jmeter.sh -Jjmeter.save.saveservice.output_format=html -Jjmeter.save.saveservice.output_format=xml -n -t src/main/jmeter/Testing.jmx -l src/main/jmeter/MyRun1.jtl"
             //step([$class: 'ArtifactArchiver', artifacts: '**/*.jtl,**/*.html'])
             //sh "mvn clean verify"
                   
         }
     }
    stage('Publish Report') {
            steps {
            
                perfReport filterRegex: '', sourceDataFiles: '**/*.jtl,**/*.html'
            
            }
        }
 
    stage('Execute Sonarqube Report') {
         steps
         {
            withSonarQubeEnv('sonar') 
             {
                sh "mvn sonar:sonar"
             }  
         }
     }
    stage('Quality Gate Check') {
         steps {
             timeout(time: 1, unit: 'HOURS') 
             {
                waitForQualityGate abortPipeline: true
            }
         }
     }

     

    }
}
