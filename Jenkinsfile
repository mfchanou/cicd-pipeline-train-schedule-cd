pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
         stage('staging') {
            when { 
                branch 'master' 
            } 
            steps {
              withCredentials([usernamePassword(credentialsId: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
                sshPublisher( 
                    continueOnError: false,
                    failOnError: true,
                    publishers: [
                        sshPublisher(
                            sshCredentials : [
                               username: '$USERNAME',
                               encryptedPassphrase:  '$USERPASS'
                            ], 
                            configName: 'staging',
                            verbose:  true,
                            transfers: [
                               sshTransfer( 
                                    sourceFiles: 'dist/trainSchedule.zip',
                                    removePrefix: 'dist',
                                    remoteDirectory: '/temp',
                                    execCommand: 'sudo /usr/bin/systemctl stop train-schedule && sudo rm -r /opt/train-schedule sudo unzip /temp/trainSchedule.zip -d /opt/train-schedule && /usr/bin/systemctl start train-schedule'
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
