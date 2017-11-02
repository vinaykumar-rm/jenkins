pipeline {
  agent any
  stages {
    stage('Clean Workspace') {
      steps {
        echo 'cleaning workspace'
      }
    }
    stage('Git Clone') {
      parallel {
        stage('Dataplatform') {
          steps {
            dir(path: 'Dataplatform') {
              git(url: 'https://github.com/riversandtechnologies/dataplatform', branch: 'master', credentialsId: '12b41655-3811-40bc-8a87-35ce9b56c3fb')
            }
            
          }
        }
        stage('RSConnect') {
          steps {
            dir(path: 'rsconnect') {
              git(url: 'https://github.com/riversandtechnologies/rsconnect.git', branch: 'master', credentialsId: '12b41655-3811-40bc-8a87-35ce9b56c3fb')
            }
            
          }
        }
        stage('RSDAM') {
          steps {
            dir(path: 'rsdam') {
              git(url: 'https://github.com/riversandtechnologies/rsdam.git', branch: 'master', credentialsId: '12b41655-3811-40bc-8a87-35ce9b56c3fb')
            }
            
          }
        }
        stage('UI') {
          steps {
            dir(path: 'ui') {
              git(url: 'https://github.com/riversandtechnologies/ui-platform.git', branch: 'master', credentialsId: '12b41655-3811-40bc-8a87-35ce9b56c3fb')
            }
            
          }
        }
        stage('devops') {
          steps {
            dir(path: 'devops') {
              git(url: 'https://github.com/riversandtechnologies/dataplatform-devops.git', branch: 'master', credentialsId: '12b41655-3811-40bc-8a87-35ce9b56c3fb')
            }
            
          }
        }
      }
    }
    stage('Build dataplatform') {
      steps {
        dir(path: 'Dataplatform/dataplatform-solution') {
          sh 'pwd'
        }
        
      }
    }
  }
}