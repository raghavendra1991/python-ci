pipeline {
  agent none
    options {
        // This is required if you want to clean before build
        skipDefaultCheckout(true)
    }
    stages {
         stage('Build') {
            steps {
                // Clean before build
                cleanWs()
                // We need to explicitly checkout from SCM here
                checkout scm
                echo "Building ${env.JOB_NAME}..."
            }
           agent { dockerfile true }
   
   	   environment {
		http_proxy = 'http://127.0.0.1:3128/'
		https_proxy = 'http://127.0.0.1:3128/'
   		ftp_proxy = 'http://127.0.0.1:3128/'
		socks_proxy = 'socks://127.0.0.1:3128/'
    	   }
	}
     
	stage('test') {
             steps {
	         sh 'python3 -m pytest'
		 sh 'python3 -m coverage xml -o coverage/coverage.xml'
	     }
	}
	stage ('Test & SonarQube Analysis') {
	    environment {
		scannerHome = tool 'SonarQube Scanner'
	    }
            steps {
		withSonarQubeEnv('admin') {
		   sh '${scannerHome}/bin/sonar-scanner \
		   -D sonar.projectKey=python-app \
		   -D sonar.python.coverage.reportPaths=coverage.xml'	
		}
            }
        }
        stage ('Archive artifacts') {
            steps {
                archiveArtifacts artifacts: 'coverage/'
            }
        }
        stage ('Publish Artifactory') {
	    steps {
		rtUpload (
		   serverId: 'JFrog',
		   spec: '''{
 			  "files" :[
			    {
		               "pattern": "/coverage",
		               "target": "pythonapp/",
	                       "recursive": "false"
			    }
		          ]
		    }'''
	        )
	    }
        }
    }	
}
