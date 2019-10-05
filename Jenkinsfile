pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
		echo "Running Build automation"
		sh "./gradlew build --no-deamon"
		archiveArtifacts artifacts: "dist/trainSchedule.zip"
            }
        }
	stage('Deploy to Staging') {
	    when {
		branch 'master'
	    }
	    steps {
		withCredentials([usernamePassword(credentialsid: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
		    sshPublisher(
			failOnError: true,
			continueOnError: false,
			publishers: [
				sshPublishersDesc(
					configname: 'staging',
					sshCredentials: [
						username: "$USERNAME",
						encryptedPassphrase: "$USERPASS"
					],
					transfers: [
						sshTransfer(
							sourceFiles: 'dist/trainSchedule.zip',
							removePrefix: 'dist/',
							remoteDirectory: '/tmp",
							execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule'
						)
					]
				)
			]
		    ) 
		}
	    }	
	}
	
	stage('Deploy to Production') {
	    when {
		branch 'master'
	    }
	    steps {
		input "Does the staging environment look good?"
		milestone(1)
		withCredentials([usernamePassword(credentialsid: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
		    sshPublisher(
			failOnError: true,
			continueOnError: false,
			publishers: [
				sshPublishersDesc(
					configname: 'production',
					sshCredentials: [
						username: "$USERNAME",
						encryptedPassphrase: "$USERPASS"
					],
					transfers: [
						sshTransfer(
							sourceFiles: 'dist/trainSchedule.zip',
							removePrefix: 'dist/',
							remoteDirectory: '/tmp",
							execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule'
						)
					]
				)
			]
		    ) 
		}
	    }	
	}
	
    }
}
