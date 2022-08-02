pipeline {
  agent {
	label 'slave'
  }
  stages { 
      stage('CleanUp WorkSpace & Git Checkout') {
          steps {
              // Clean before build
              cleanWs()
              // We need to explicitly checkout from SCM here
              checkout scm
              echo "Building ${env.JOB_NAME}..."
          }
      }	  
      stage('Build & Test') {
         agent {
	     dockerfile {
      	        filename 'Dockerfile'
   	        reuseNode true
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
      post {
	 always {
	     echo "Project Succeeded"
	 }
      }	  
   }
}
