pipeline {
   agent none
   environment {
      http_proxy = 'http://127.0.0.1:3128/'
      https_proxy = 'http://127.0.0.1:3128/'
      ftp_proxy = 'http://127.0.0.1:3128/'
      socks_proxy = 'socks://127.0.0.1:3128/'
   }
   stages {
      stage ('Checkout') {
	  agent { 
	     label 'slave'
	  }
          steps {
	     echo "Checkout slave agent"
	  }
      }
      stage ('Build') {
	      agent 'slave'{
		      { dockerfile true}
	      }
	  steps {
	      sh 'python3 -m pytest'
	  }
      }
   }	
}
