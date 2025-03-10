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
      stage('Mutation Tests - PIT') {
            steps {
               sh "mvn org.pitest:pitest-maven:mutationCoverage"
              }
            post {
               always {
                  pitmutation mutationStatsFile: '**/target/pit-reports/**/mutations.xml'
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
      stage('Kubernetes Deployment - Dev') {
            steps {
                 withKubeConfig([credentialsId: 'kubeconfig']) {
                     sh "sudo chmod 777 /home/yogi/k8s_deployment_service.yaml"
                     sh "sudo sed -i 's#replace#odurusai/yogi:${GIT_COMMIT}#g' /home/yogi/k8s_deployment_service.yaml"
                     sh "kubectl apply -f /home/yogi/k8s_deployment_service.yaml"
                 }
            }
      }
    }
}
