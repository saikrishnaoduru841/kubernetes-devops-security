pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar' // so
            }
        }
      stage('Unit Test - Jacoco') {
            steps {
              sh "mvn test"
            }
            post {
              always {
                 junit 'target/surefire-reports/*.xml'
                 jacoco execPattern: 'target/jacoco.exec'
              }   
            }  
      }
      stage('Docker build and Push') {
            steps {
                 withDockerRegistry([credentialsId: "docker-hub", url: ""]) {
                     sh 'printenv'
                     sh 'docker build -t odurusai/yogi:""\$GIT_COMMIT"" .'
                     sh 'docker push odurusai/yogi:""\$GIT_COMMIT""'
                 }
            }
      }
    }
}
