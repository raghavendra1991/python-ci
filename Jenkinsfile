pipeline {
  agent none
  options {
        // This is required if you want to clean before build
        skipDefaultCheckout(true)
  }
  stages {
      stage('CleanUp WorkSpace') {
	  agent { 
	      label 'slave'
	  }
          steps {
              // Clean before build
              cleanWs()
              // We need to explicitly checkout from SCM here
              checkout scm
              echo "Building ${env.JOB_NAME}..."
          }
	  post {
              // Clean after build
              always {
		 echo "I Succeeded"
              }
          }
      }	  
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
	 post {
	      always {
		   archiveArtifacts artifacts: 'coverage/'
      	      }
	 }
      }	 
      stage('Code Analysis') {
	  agent {
     	       label 'slave'
    	  }
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
	  post {
	     always {
		   echo "I Succeeded"
	     }
	  }
      }
      stage('Deploy Atrifacts') {
	  agent {
     	       label 'slave'
    	  }
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
	  post {
	     always {
		   echo "I Succeeded"
	     }
	  }
      }
   }
}
