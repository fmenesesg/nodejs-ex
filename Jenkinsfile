//def templatePath = 'https://raw.githubusercontent.com/openshift/nodejs-ex/master/openshift/templates/nodejs-mongodb.json' 
def BUILD_NAME = 'nodejs-example'
def NS_NAME = "fm-pelorus-nodejs"
def BUILD = openshift.project()
def NS_DEV = "fm-pelorus-dev"
def NS_STAGE = "fm-pelorus-stage"
def NS_PROD = "fm-pelorus-prod"

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
                  def builds = openshift.selector("bc", BUILD_NAME).related('builds')
                  timeout(5) { 
                    builds.untilEach(1) {
                      return (it.object().status.phase == "Complete")
                    }
                  }
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
                  openshift.tag("${BUILD}/${BUILD_NAME}:latest", "${NS_DEV}/${BUILD_NAME}-dev:latest")
                  //openshift.openshiftTag srcStream: '${BUILD_NAME}',   namespace: '${BUILD}', srcTag: 'latest', destinationNamespace: '${NS_DEV}',destStream: '${BUILD_NAME}', destTag: 'dev' 
                }
            }
        }
      }
    }
  }
}



