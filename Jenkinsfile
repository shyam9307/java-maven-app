pipeline {
    agent any

    tools {
        jdk 'JDK17'
        maven 'Maven3'
    }

    stages {

        stage('Compile') {
            steps {
                dir('java-maven-app') {
                    sh 'pwd'
                    sh 'ls -la'
                    sh 'find . -maxdepth 3'
                    sh 'mvn clean compile'
                }
            }
        }

        // stage('Compile') {
        //     steps {
        //         dir('java-maven-app') {
        //             sh 'mvn clean compile'
        //         }
        //     }
        // }

        stage('Test') {
            steps {
                dir('java-maven-app') {
                    sh 'mvn test'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                dir('java-maven-app') {
                    withSonarQubeEnv('SonarQube') {
                        sh '''
                        mvn sonar:sonar \
                        -Dsonar.projectKey=java-maven-demo \
                        -Dsonar.host.url=$SONAR_HOST_URL \
                        -Dsonar.token=$SONAR_AUTH_TOKEN
                        '''
                    }
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Package') {
            steps {
                dir('java-maven-app') {
                    sh 'mvn package'
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline executed successfully!'
            // cleanWs()
        }

        failure {
            echo 'Pipeline failed.'
        }
    }
}
