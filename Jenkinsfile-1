pipeline {
  agent any

  tools {
    jdk   'JDK 21'
    maven 'Maven 3.9.9'
  }

  environment {
    // Identifiant de la credential "sonar-token" créé en étape 5
    SONAR_TOKEN = credentials('sonar-token')
    SONAR_PROJECT_KEY = 'demo-java'
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
                -Dsonar.projectKey=${env.SONAR_PROJECT_KEY} \
                -Dsonar.host.url=${env.SONAR_HOST_URL} \
                -Dsonar.login=${env.SONAR_TOKEN}"
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
