node{
   stage('SCM Checkout'){
     git 'https://github.com/Anup-Nandan/my-app.git'
   }
   stage('Compile-Packages'){

      def mvnHome =  tool name: 'maven3', type: 'maven'   
      sh "${mvnHome}/bin/mvn clean package"
	  sh 'mv target/myweb*.war target/newapp.war'
   }
   stage('SonarQube Analysis') {
	        def mvnHome =  tool name: 'maven3', type: 'maven'
	        withSonarQubeEnv('sonar') { 
	          sh "${mvnHome}/bin/mvn sonar:sonar"
	        }
	    }
   stage('Build Docker Images'){
   sh 'docker build -t saidamo/myweb:0.0.2 .'
   }
   stage('Docker Image Pushed'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u saidamo -p ${dockerPassword}"
    }
   sh 'docker push saidamo/myweb:0.0.2'
   }
   stage('Nexus Image Push'){
   sh "docker login -u admin -p admin123 3.110.123.32:8083"
   sh "docker tag saidamo/myweb:0.0.2 3.110.123.32:8083/damo:1.0.0"
   sh 'docker push 3.110.123.32:8083/damo:1.0.0'
   }
   stage('Removing Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}
   stage('Final Docker deployments'){
   sh 'docker run -d -p 8090:8080 --name tomcattest saidamo/myweb:0.0.2' 
   }
}
}
