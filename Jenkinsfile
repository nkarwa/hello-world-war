node{
   stage('Checkout'){
       //git url:"https://github.com/cndacademy/hello-world-war.git", branch:"master", credentialsId: 'github-credentials'
       checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/cndacademy/hello-world-war.git']]])
       sh "pwd"
   }
     stage('Sonar Analysis'){
        withSonarQubeEnv('sonar'){
            def mvnHome = tool 'maven'
            sh "'${mvnHome}'/bin/mvn sonar:sonar"
            sh 'sleep 1m'
   }
   }
}
