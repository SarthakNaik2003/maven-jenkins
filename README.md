
pipeline {

    agent { label 'sarthak' } 

    stages {

        stage('Checkout Code') {

            steps {

                git branch: 'master', url: 'https://github.com/SarthakNaik2003/maven-jenkins.git'

            }

        }

        stage('Build') {

            steps {

                bat 'mvn clean install' // Use bat for Windows commands

            }

        }

        stage('Create ZIP File') {

            steps {

                // Option 1: Using 7-Zip

                bat '"C:\\Program Files\\7-Zip\\7z.exe" a -tzip target.zip target\\' // Adjust path if needed

                // Option 2: Using PowerShell

                // powershell "Compress-Archive -Path target\\* -DestinationPath target.zip"

            }

        }

        stage('Archive Artifacts') {

            steps {

                archiveArtifacts artifacts: 'target/*.jar', allowEmptyArchive: true

                archiveArtifacts artifacts: 'target.zip', allowEmptyArchive: true 

            }

        }

        stage('Deploy') {

            steps {

                script {

                    bat """
                        REM  curl -v -u admin:boss007 -X PUT -T target.zip http://localhost:8081/repository/com/example/maven-jenkins/maven-jenkins.zip
                            curl -v -u admin:boss007 -X PUT -T target.zip http://localhost:8081/repository/raw-hosted/com/example/maven-jenkins/maven-jenkins.zip

                        REM curl -v -u admin:Tandel@1202 -X PUT -T target.zip http://localhost:8081/repository/simple-project/org/springframework/jb-hello-world-maven/0.2.0/jb-hello-world-maven-0.2.2.zip
                    """

                }

            }

        }

    }

    post {

        success {

            echo "Build successful!"

        }

        failure {

            echo "Build failed!"

        }

    }

}
