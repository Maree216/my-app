node{
   stage('SCM Checkout'){
     git 'https://github.com/Maree216/my-app.git'
   }
   stage('Compile-Package'){

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
   stage('Build Docker Imager'){
   sh 'docker build -t mareeg/myweb:0.0.2 .'
   }
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerimage', variable: 'dockerPassword')]) {
   sh "docker login -u mareeg -p ${dockerPassword}"
    }
   sh 'docker push mareeg/myweb:0.0.2'
   }
   stage('Nexus Image Push'){
   sh "docker login -u admin -p maree321 15.206.165.136:8083"
   sh "docker tag mareeg/myweb:15.206.165.136:8083/maree:1.0.0"
   sh 'docker push 15.206.165.136:8083/maree:1.0.0'
   }
   stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}
   stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name tomcattest mareeg/myweb:0.0.2' 
   }
   }
}
