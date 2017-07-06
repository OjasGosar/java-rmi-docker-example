node("ec2") {

  def application
  
  stage('Clone repository') {
    checkout scm
  }
  
  stage('Package') {
    dir('app') {
      sh 'mvn clean package -DskipTests'
    }
  }

  stage('Build image') {
    dir('app') {
      application = docker.build("ojasgosar/java-rmi-docker:${env.BUILD_NUMBER}")
    }
  }

  stage('Run Tests') {
    try {
      dir('app') {
        sh "mvn test"
      }
    } catch (error) {
      throw error
    } finally {
      junit '**/target/surefire-reports/*.xml'
    }
  }

  stage('Push image') {

    docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
            application.push("${env.BUILD_NUMBER}")
            application.push("latest")
    }
  }
}