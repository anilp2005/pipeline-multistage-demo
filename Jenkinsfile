pipeline {
  agent { node { label 'master' } }
  parameters {
    booleanParam(name: 'DEPLOYS', defaultValue: false, description: 'Proceed for Deployment?.')
 }
  triggers {
    pollSCM('H/10 * * * * ')
  }
  environment {
    JAVA_HOME="${tool 'Java8'}"
    PATH="${env.JAVA_HOME}/bin:${env.PATH}"
  }
  tools {
    maven "Maven3"
    jdk "Java8"
  }


  stages {

      stage('Prepare') {
      steps {
        git branch: "master", url: "https://github.com/devopsguru91/MavenCounterWebApp.git"
      }
    }


  stage('Build') {
      steps {
        sh "mvn -Dmaven.test.failure.ignore clean package"
      }
    }

    stage('Approve deployment on Dev environment') {
      when {
        expression { return params.DEPLOYS }
      }
      steps {
        timeout(time: 1, unit: 'HOURS') {
          script {
            env.DEPLOY_DEV = input message: 'Approve deployment', parameters: [
              [$class: 'BooleanParameterDefinition', defaultValue: false, description: '', name: 'Approve deployment on Dev environment']
            ]
          }
        }
      }
    }

    stage('Deploy on Dev environment') {
      when {
        environment name: 'DEPLOY_DEV', value: "true"
      }
      steps {
        
        echo "Deployed on Dev Envirnment. Invoke Ansible Playbook"

      }
    }

    stage('Approve deployment on QA') {
      when {
        environment name: 'DEPLOY_DEV', value: "true"
      }
      steps {
        timeout(time: 1, unit: 'DAYS') {
          script {
           env.DEPLOY_QA = input message: 'Approve deployment', parameters: [
              [$class: 'BooleanParameterDefinition', defaultValue: false, description: '', name: 'Approve deployment on QA']
            ]
          }
        }
      }
    }

    stage('Deploy on QA environment') {
      when {
        environment name: 'DEPLOY_QA', value: "true"
      }
      steps {
        
        echo "Deployed on QA Envirnment. Invoke Ansible Playbook"

      }
    }

    stage('Approve deployment on UAT') {
      when {
        environment name: 'DEPLOY_QA', value: "true"
      }
      steps {
        timeout(time: 3, unit: 'DAYS') {
          script {
           env.DEPLOY_UAT = input message: 'Approve deployment', parameters: [
              [$class: 'BooleanParameterDefinition', defaultValue: false, description: '', name: 'Approve deployment on UAT']
            ]
          }
        }
      }
    }

    stage('Deploy on UAT environment') {
      when {
        environment name: 'DEPLOY_UAT', value: "true"
      }
      steps {
        
        echo "Deployed on UAT Envirnment. Invoke Ansible Playbook"

      }
    }
  }
}
