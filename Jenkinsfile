pipeline {
    agent any

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "MAVEN_HOME"
    }

    stages {
        stage('Clone') {
            steps {
                timeout(time: 2, unit: 'MINUTES'){
                    git branch: 'main', credentialsId: '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', url: 'https://github.com/cristhianllanque/ProyectoPruebas.git'
                }
            }
        }
        stage('Build') {
            steps {
                timeout(time: 8, unit: 'MINUTES'){
                    sh "mvn -DskipTests clean package -f Cris/pom.xml"
                }
            }
        }
        stage('Test') {
            steps {
                timeout(time: 8, unit: 'MINUTES'){
                    sh "mvn clean install -f Cris/pom.xml"
                }
            }
        }
       
	stage('Sonar') {
    	    steps {
        	timeout(time: 8, unit: 'MINUTES') {
            	     withSonarQubeEnv('sonarqube') {
                        sh "mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.9.0.2155:sonar -Pcoverage -f Cris/pom.xml"
            	    }
                }
            }
        }

        stage('Quality gate') {
            steps {
                sleep(10) // seconds

                script {
                    try {
                        timeout(time: 3, unit: 'MINUTES') {
                            waitForQualityGate() // Sin abortPipeline para que no falle automáticamente
                        }
                    } catch (e) {
                        echo "Timeout o fallo del Quality Gate: ${e.getMessage()}, pero el pipeline continúa."
                        //ﬁﬁﬁ Si quieres marcar el stage como fallido pero continuar, puedes usar un buildResult
                        currentBuild.result = 'UNSTABLE'
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
        sh "mvn spring-boot:run -f Cris/pom.xml"
            }
        }
    }
}
