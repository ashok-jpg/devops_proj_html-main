pipeline {
    agent any 

    stages {
        stage('Build') {
            steps {
                echo "Build Process"
            }
        }

         stage('SonarQube analysis') {
             steps {
                withSonarQubeEnv('SonarQubeServer') {
                     script {
        //                 def scannerHome = tool 'sonar';
        //                 withEnv(["PATH+SONAR=${scannerHome}/bin"]) {
        //                     sh 'sonar-scanner -Dsonar.projectKey=${sonar.project.key} -Dsonar.sources=. -Dsonar.host.url=${sonar.server.url} -Dsonar.login=${sonar.token}'
        //                 }
        //             }
        //         }
        //     }
        // }


      

        stage('Package') {
            steps {
               // Package both the HTML file and the images folder
                //sh 'tar -czf my-html-project.tar.gz index.html images/'
                sh 'tar -czf myapp.tar.gz index.html'  
                // Archive the artifact for download from Jenkins UI
                archiveArtifacts artifacts: 'myapp.tar.gz', allowEmptyArchive: false
            }
        }

        stage('Create Docker Image') {
            steps {
                sh 'docker build -t myapp:latest .'
            }
        }

        stage('Deploy Docker Image') {
            steps {
                sshagent(['deploy_key']) { //// replace 'my-ssh-key-id' with your actual credential ID
                    sh 'docker save myapp:latest | ssh admin1@172.28.12.198 "docker load"'
                }
            }
        }

  stage('Stop and Remove Existing Docker Container') {
            steps {
                sshagent(['deploy_key']) {
                    sh 'ssh admin1@172.28.12.198 "docker stop myapp_container && docker rm myapp_container || true"'
                }
            }
        }



        stage('Run Docker Container') {
            steps {
                sshagent(['deploy_key']) { // replace 'my-ssh-key-id' with your actual credential ID
                    
                 
                    
                    sh '''
                        ssh admin1@172.28.12.198 'docker run -d -p 8083:80 --name myapp_container myapp:latest'

                    '''
                }
            }
        }
    }
}
