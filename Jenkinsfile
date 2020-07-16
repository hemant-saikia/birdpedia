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
            steps {
                sshPublisher(
                    continueOnError: false, failOnError: true,
                    publishers: [
                        sshPublisherDesc(
                                configName: "Zerund",
                                verbose: true,
                                transfers: [
                                sshTransfer(
                                sourceFiles: "workspace/birdpedia ci/birdpedia",
                                removePrefix: "workspace/birdpedia ci",
                                remoteDirectory: "/",
                                execCommand: "pwd"
                            )
                        ])
                    ])
            }
        }
        stage ('Deploy') {
            steps{
                sshagent(credentials : ['00437793-dff8-41aa-a227-66e788ab9990']) {
                    sh 'ssh -o StrictHostKeyChecking=no ubuntu@ec2-3-6-77-14.ap-south-1.compute.amazonaws.com uptime'
                    sh 'ssh -v ubuntu@ec2-3-6-77-14.ap-south-1.compute.amazonaws.com'
                    sh 'scp /Users/hemant/testci.sh ubuntu@ec2-3-6-77-14.ap-south-1.compute.amazonaws.com:/home/ubuntu/test'
                }
            }
        }
        stage('Done'){
            steps{
                echo 'Done with builds and tests'
            }	
        }
        
    }
}
