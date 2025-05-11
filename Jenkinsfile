pipeline {
  agent any

  tools {
    jdk   'JDK 21'       // Nom exact vu dans Manage Jenkins → Global Tool Configuration
    maven 'Maven 3.9.9'  // idem
  }

  environment {
    SONAR_TOKEN       = credentials('sonar-token')   // ID de la Secret text credential
    SONAR_PROJECT_KEY = 'demo-java'                 // Clé projet SonarQube
    SONAR_HOST_URL    = 'http://sonarqube:9000'
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build & Test') {
      steps {
        sh './mvnw clean verify'
      }
    }

    stage('SonarQube Analysis') {
      steps {
        withSonarQubeEnv('SonarQube') {
          sh "./mvnw sonar:sonar \
                -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
                -Dsonar.host.url=${SONAR_HOST_URL} \
                -Dsonar.login=${SONAR_TOKEN}"
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
  }

  post {
    success {
      echo '✅ Analyse SonarQube réussie !'
    }
    failure {
      echo '❌ Échec du Quality Gate : consultez SonarQube pour les détails.'
    }
  }
}
