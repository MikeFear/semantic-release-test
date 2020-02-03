DOCKER_MAVEN_ARGS = '-v semantic-release-maven-repository:/home/jenkins/.m2'

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
}
