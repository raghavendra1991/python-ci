pipeline {
  agent none
  stages { 	  
      stage('Build & Test') {
         agent {
	     dockerfile {
      	        filename 'Dockerfile'
   	        label 'slave'
	     }	        
	 }
	 steps {
	      sh 'python3 -m pytest'
	      sh 'python3 -m coverage xml -o coverage/coverage.xml'
	 }
      }	 
      stage('Code Analysis') {
	  environment {
	       scannerHome = tool 'SonarQube Scanner'
	  }
	  steps {
	      withSonarQubeEnv('admin') {
		   sh '${scannerHome}/bin/sonar-scanner \
		      -D sonar.projectKey=reports \
		      -D sonar.python.coverage.reportPaths=coverage.xml'
	      }
          }
      }
      stage('Deploy Atrifacts') {
	  steps {
	      rtUpload (
		 serverId: 'JFrog',
		 spec: '''{
 			"files" :[
			  {
		            "pattern": "coverage/",
		            "target": "python-ci/",
	                    "recursive": "false"
	         	  }
		        ]
		 }'''
	     )
	 }
      }
   }
   post {
	 always {
	     echo 'I have finished'
	 }
	 success { 
	     echo 'I succeeded!'
	 }
	 unstable {
	     echo 'I am unstable :/'
	 }
	 failure {
             echo 'I failed :('
	 }
	 changed {
	     echo 'Things are different ...'
	 }
   }	  
}
