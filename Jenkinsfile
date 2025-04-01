pipeline {
  agent any

  tools {
    python 'python:3.13'
  }

  environment {
    SONARQUBE_SERVER = 'sonarqube'
    SONAR_SCANNER_HOME = '/var/jenkins_home/tools/hudson.plugins.sonar.SonarRunnerInstallation/SonarScanner'
  }

  stages {
    stage('Instalar dependencias') {
      steps {
        sh 'python -m venv venv'
        sh 'source venv/bin/activate'
        sh 'pip install -r requirements.txt'
      }
    }
    
    stage('Ejecutar Pruebas y Cobertura') {
      steps {
        sh 'source venv/bin/activate && pytest --cov=src --cov-report=xml'
      }
    }    

    stage('Análisis SonarQube') {
      steps {
        withSonarQubeEnv("${SONARQUBE_SERVER}") {
          withCredentials([string(credentialsId: 'sonarqube_auth_token', variable: 'SONAR_TOKEN')]) {
            sh """
              \${SONAR_SCANNER_HOME}/bin/sonar-scanner \\
                -Dsonar.projectKey=asignacion-frontend \\
                -Dsonar.sources=src \\
                -Dsonar.host.url=http://sonarqube:9000 \\
                -Dsonar.token=\${SONAR_TOKEN} \\
                -Dsonar.javascript.lcov.reportPaths=coverage/lcov.info \\
                -Dsonar.eslint.reportPaths=eslint-report.json
            """
          }
        }
      }
    }
  }
}
