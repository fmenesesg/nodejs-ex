//def templatePath = 'https://raw.githubusercontent.com/openshift/nodejs-ex/master/openshift/templates/nodejs-mongodb.json' 
def BUILD_NAME = 'pelorus-nodejs'
def NS_NAME = "fm-pelorus-nodejs"
def BUILD = openshift.project()
def NS_DEV = "pelorus-nodejs-dev"
def NS_STAGE = "pelorus-nodejs-stage"
def NS_PROD = "pelorus-node-prod"

pipeline {
  agent {
    node {
      label 'nodejs' 
    }
  }
  options {
    timeout(time: 20, unit: 'MINUTES') 
  }
  stages {
    stage('preamble') {
        steps {
            script {
                openshift.withCluster() {
                    openshift.withProject() {
                        echo "Using project: ${openshift.project()}"
                    }
                }
            }
        }
    }
    /*stage('cleanup') {
      steps {
        script {
            openshift.withCluster() {
                openshift.withProject() {
                  openshift.selector("all", [ template : templateName ]).delete() 
                  if (openshift.selector("secrets", templateName).exists()) { 
                    openshift.selector("secrets", templateName).delete()
                  }
                }
            }
        }
      }
    }*/
    /*stage('create') {
      steps {
        script {
            openshift.withCluster() {
                openshift.withProject() {
                  openshift.newApp(templatePath) 
                }
            }
        }
      }
    }*/
    stage('build') {
      steps {
        script {
            openshift.withCluster() {
                openshift.withProject() {
                  def builds = openshift.selector("bc", BUILD_NAME)
                  builds.startBuild()
                  sleep 90
                }
            }
        }
      }
    }
    stage('tag') {
      steps {
        script {
            openshift.withCluster() {
                openshift.withProject() {
                  sleep 15
                  openshift.tag("pelorus-nodejs-build/${BUILD_NAME}:latest", "${NS_DEV}/${BUILD_NAME}-dev:latest")
                  //openshift.openshiftTag srcStream: '${BUILD_NAME}',   namespace: '${BUILD}', srcTag: 'latest', destinationNamespace: '${NS_DEV}',destStream: '${BUILD_NAME}', destTag: 'dev' 
                }
            }
        }
      }
    }
  }
}



