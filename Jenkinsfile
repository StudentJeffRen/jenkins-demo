node('haimaxy-jnlp') {
  stage('Prepare') {
    echo "1.Prepare"
    checkout scm
    script {
        build_tag = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
        if (env.BRANCH_NAME != 'master') {
            build_tag = "${env.BRANCH_NAME}-${build_tag}"
        }
    }
  }

  stage('Test') {
      echo "2.Test Stage"
  }
  
  stage('Build') {
    echo "3.Build Docker Image Stage"
    sh "docker build -t jeffren/jenkins-demo:${build_tag} ."
  }
  
  stage('Push') {
    echo "4.Push Docker Image Stage"
    withCredentials([usernamePassword(credentialsId: 'DOCKER_HUB', passwordVariable: 'DOCKER_HUBPassword', usernameVariable: 'DOCKER_HUBUser')]) {
        sh "docker login -u ${DOCKER_HUBUser} -p ${DOCKER_HUBPassword}"
        sh "docker push jeffren/jenkins-demo:${build_tag}"
    }
  }

  stage('Deploy') {
    echo "5. Deploy Stage"
    
    if (env.BRANCH_NAME == 'master') {
        input "Are you sure to deploy online environmen?"
    }
    
    sh "sed -i 's/<BUILD_TAG>/${build_tag}/' k8s.yaml"
    sh "sed -i 's/<BRANCH_NAME>/${env.BRANCH_NAME}/' k8s.yaml"
    sh "kubectl apply -f k8s.yaml"
  }
}
