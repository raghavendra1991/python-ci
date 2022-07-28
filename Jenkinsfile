pipeline {
   agent none
   environment {
      http_proxy = 'http://127.0.0.1:3128/'
      https_proxy = 'http://127.0.0.1:3128/'
      ftp_proxy = 'http://127.0.0.1:3128/'
      socks_proxy = 'socks://127.0.0.1:3128/'
   }
   stages {
      stage('Build') {
	  agent { label 'slave' { dockerfile true }}  
          steps {
	     echo "hello world"
	  }
      }
   }	
}
