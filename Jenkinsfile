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
      stage('Kubernetes Deployment - Dev') {
            steps {
                 withKubeConfig([credentialsId: 'kubeconfig']) {
                     // sh "kubectl create deploy node-app --image siddharth67/node-service:v1"
                     // sh "kubectl expose deploy node-app --name node-service --port 5000"
                     sh "sed -i 's#replace#odurusai/yogi:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
                     sh "kubectl apply -f k8s_deployment_servcie.yaml"
                 }
            }
      }
    }
}
