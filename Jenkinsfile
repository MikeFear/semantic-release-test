DOCKER_MAVEN_ARGS = '-v autopilot-maven-repository:/home/jenkins/.m2'

pipeline {
    agent any

    options {
        timeout(time: 1, unit: 'HOURS')
        buildDiscarder(logRotator(numToKeepStr: '25'))
        disableConcurrentBuilds()
    }

    triggers {
        pollSCM('* * * * *')
    }

    stages {
        stage('Build and test') {
            agent {
            	dockerfile {
					dir 'jenkins'
					args DOCKER_MAVEN_ARGS
				}
          	}
            stages {
                stage('Build') {
                    steps {
                        sh 'mvn package -DskipTests'
                        stash includes: 'target/*.jar', name: 'artifacts'
                    }
                }

                stage('Test') {
                    steps {
                        sh 'mvn test -Dmaven.test.failure.ignore=true'
                        stash includes: '**/target/surefire-reports/*.xml', name: 'testResults'
                    }
                }
            }
        }
        stage('Publish new version') {
        	agent {
				dockerfile {
					dir 'jenkins'
					args DOCKER_MAVEN_ARGS
				}
			}
			steps {
				sshagent(credentials: ['f4808ddb-0310-4fe8-88e3-c64664195543']) {
					sh 'npm ci'
					sh 'npx semantic-release'
				}
			}
        }
    }

    post {
        always {
            unstash 'artifacts'
            unstash 'testResults'
            archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            junit '**/target/surefire-reports/*.xml'
        }
    }
}
