pipeline {
    agent any
    tools {
        go 'Go1.14'
    }
    environment {
        GO114MODULE = 'on'
        CGO_ENABLED = 0 
        GOPATH = "${JENKINS_HOME}/jobs/${JOB_NAME}/builds/${BUILD_ID}"
    }
    stages {        
        stage('Pre Test') {
            steps {
                echo 'Installing dependencies'
                sh 'go version'
                sh 'go get -d ./'
            }
        }
        
        stage('Build') {
            steps {
                echo 'Compiling and building'
                sh 'go build'
            }
        }

        stage('Test') {
            steps {
                withEnv(["PATH+GO=${GOPATH}/bin"]){
                    echo 'Running vetting'
                    sh 'go vet .'
                    echo 'Running test'
                    sh 'go test -v'
                }
            }
        }

	stage('SSH transfer') {
	 script {
	  sshPublisher(
	   continueOnError: false, failOnError: true,
	   publishers: [
	    sshPublisherDesc(
	     configName: "${env.SSH_CONFIG_NAME}",
	     verbose: true,
	     transfers: [
	      sshTransfer(
	       sourceFiles: "${path_to_file}/${file_name}, ${path_to_file}/${file_name}",
	       removePrefix: "${path_to_file}",
	       remoteDirectory: "${remote_dir_path}",
	       execCommand: "run commands after copy?"
	      )
	     ])
	   ])
	 }
	}
	stage('Done'){
		steps{
			echo 'Done with builds and tests'
		}	
	}
        
    }
}
