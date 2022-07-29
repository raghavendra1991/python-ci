pipeline {
   agent { dockerfile true }
   environment {
	http_proxy = 'http://127.0.0.1:3128/'
	https_proxy = 'http://127.0.0.1:3128/'
	ftp_proxy = 'http://127.0.0.1:3128/'
	socks_proxy = 'socks://127.0.0.1:3128/'
   }
    stages {
	stage('test') {
             steps {
	         sh 'python3 -m pytest'
		 sh 'python3 -m coverage xml -o coverage/coverage.xml'
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
		               "pattern": "coverage/",
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
