node{
    try{
   stage('Checkout'){
       //git url:"https://github.com/cndacademy/hello-world-war.git", branch:"master", credentialsId: 'github-credentials'
       checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/cndacademy/hello-world-war.git']]])
       sh "pwd"
   }
     stage('Sonar Analysis'){
        withSonarQubeEnv('SonarCloud'){
            def mvnHome = tool 'maven3.3'
            sh "'${mvnHome}'/bin/mvn sonar:sonar"
            sh 'sleep 1m'
   }
   }
    stage("Quality Gate Result"){
      timeout(time: 15, unit: 'MINUTES') {
      def qg = waitForQualityGate()
      if (qg.status != 'OK') {
         currentBuild.result = "FAILURE"
         error "Pipeline aborted due to quality gate failure: ${qg.status}"
          }
       }
      }
     
      
    
      stage("Buid"){
          
          sh 'mvn package -Dmaven.test.skip=true'
          archiveArtifacts 'target/hello-app.war'
      }
      stage("Test"){
          sh 'mvn test'
      }
      stage("Publish Artifacts Nexus"){
          
          nexusPublisher nexusInstanceId: 'Nexus3', nexusRepositoryId: 'maven-releases', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath: 'target/hello-app.war']], mavenCoordinate: [artifactId: 'hello-world-war', groupId: 'com.efsavage', packaging: 'war', version: '5.2']]]
          
          
      }
      stage("Deploy Dev"){
          
          //logic Nexus
          
          sh 'sudo cp target/hello-app.war /var/lib/tomcat8/webapps'
      }
      stage('Approval to deploy Prod'){
	    timeout(time: 15, unit: 'MINUTES'){
	    input message: 'Do you approve deployment for production?' , ok: 'Yes'
	    }
	    stage("Deploy Prod"){
	        
	        //Download logic from Nexus
	        sshPublisher(publishers: [sshPublisherDesc(configName: 'prod-server', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: '', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: 'target', sourceFiles: 'target/hello-app.war')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
	    }
	    
	}
      currentBuild.result = 'SUCCESS'
	}
   catch(err)
  {
    currentBuild.result = 'FAILURE'
    }
  finally{
    mail to:"ajitgarad333@gmail.com",
             subject: "Status of pipeline: ${currentBuild.fullDisplayName}",
             body: "${env.BUILD_URL} has result ${currentBuild.result} " 
}
}
