pipeline {
    agent any
    stages {
        stage('Test') {
            steps {
                bat './gradlew test'
                junit 'build/test-results/**/*.xml'
            }
        }


       stage('Generate HTML report') {
         steps {
               cucumber buildStatus: 'UNSTABLE',
                       reportTitle: 'My report',
                       fileIncludePattern: 'reports/*.json',
                       trendsLimit: 10

                         }

           }

        /*stage('Analyse du Code') {
            steps {
                withSonarQubeEnv('sonar') {
                    bat './gradlew sonarqube'
                }
            }
        }*/

        /*stage('Code Quality') {
            steps {
                script {
                    echo "Vérification Quality Gate"
                    def qg = waitForQualityGate()
                    if (qg.status != 'OK') {
                        error "Quality Gate échoué: ${qg.status}"
                    } else {
                        echo "Quality Gate OK"
                    }
                }
            }
        }*/
         stage('Build') {
                    steps {
                        bat './gradlew clean build'
                        bat './gradlew javadoc'
                        archiveArtifacts 'build/libs/*.jar'
                        archiveArtifacts 'build/docs/javadoc/**'
                    }
                }

        stage('Deploy') {
            steps {
                bat './gradlew publish'
            }
        }

        stage('Notification') {
            steps {
                 slackSend(
                            channel: '#tp-jenkins',
                            message: " Déploiement réussi",
                            color: 'good',
                            tokenCredentialId: 'slack-bot-token'
                        )
            }
        }
    }
    post {
        always {
            echo "Phase Test terminée"
        }

        success {
            echo "Tous les tests ont réussi"
            emailext(
                        subject: " Build réussi: ",
                        body: "Le build a réussi.",
                        to: "mk_foudili@esi.dz"
                   )
        }
        failure {
            echo "Échec dans une ou plusieurs phases"
            slackSend(
                channel: '#tp-failure',
                color: 'danger',
                message: "Pipeline échoué !",
                tokenCredentialId: 'slack-bot-token'
            )
        }
    }
}
