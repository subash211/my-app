node{
   stage('SCM Checkout'){
     checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/subash211/my-app.git']])
   }
    
    stage('build-stage-maven'){
      def mvnHome =  tool name: 'maven', type: 'maven'   
      sh "${mvnHome}/bin/mvn clean package"
	  sh 'mv target/myweb*.war target/newapp.war'
   }
   stage('SonarQube Analysis') {
	   def mvnHome =  tool name: 'maven', type: 'maven'
	   withSonarQubeEnv('sonar') { 
	   sh "${mvnHome}/bin/mvn sonar:sonar"
	        }
	    }
	    stage('Build-Docker-Image'){
        sh 'docker build -t subash211/maven-build .'
   }
	stage('Docker-Image-Push'){
        withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
        sh "docker login -u subash211 -p ${dockerPassword}"
    }
        sh 'docker push subash211/maven-build'
   }
    stage('Nexus Image Push'){
        sh "docker login -u admin -p subash123 13.232.180.224:8083"
        sh "docker tag subash211/maven-build 13.232.180.224:8083/subash:1.0.0"
        sh 'docker push 13.232.180.224:8083/subash:1.0.0'
   }
   stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}
   }
    stage('Docker deployment'){
   sh 'docker run -d -p 8000:8090 --name tomcattest subash211/maven-build' 
   }
}



