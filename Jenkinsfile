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
                echo "${GOPATH}"
                sh 'go version'
                sh 'go get -d ./'
            }
        }
        
        stage('Build') {
            steps {
                echo 'Compiling and building'
                sh 'env GOOS=linux go build'
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
       
        stage ('Deploy') {
            steps{
                    sshagent(credentials : ['00437793-dff8-41aa-a227-66e788ab9990']) {
                    sh 'ssh ubuntu@ec2-3-6-77-14.ap-south-1.compute.amazonaws.com  "date"'

                    sh 'ssh ubuntu@ec2-3-6-77-14.ap-south-1.compute.amazonaws.com "mv /home/ubuntu/hemant/backup/asterisk_ari* /home/ubuntu/hemant/backup_archives/"'

                    sh 'ssh ubuntu@ec2-3-6-77-14.ap-south-1.compute.amazonaws.com "cp /home/ubuntu/asterisk/asterisk-ari /home/ubuntu/hemant/backup/asterisk_ari_$(date +"%Y_%m_%d_%I_%M_%p")"'

                    sh 'ssh ubuntu@ec2-3-6-77-14.ap-south-1.compute.amazonaws.com  "sudo systemctl stop asterisk-ari"'

                    sh 'scp birdpedia ubuntu@ec2-3-6-77-14.ap-south-1.compute.amazonaws.com:/home/ubuntu/asterisk/asterisk-ari'

                    sh 'ssh ubuntu@ec2-3-6-77-14.ap-south-1.compute.amazonaws.com  "sudo systemctl start asterisk-ari"'
                }
            }
        }
        stage('Done'){
            steps{
                echo 'Done with builds and tests'
            }	
        }
        
    }
    post{
    success {
      slackSend (color: '#00FF00', message: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})  BUILD_LOG_EXCERPT")
    }
    failure {
      slackSend (color: '#FF0000', message: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
    }
}
}
