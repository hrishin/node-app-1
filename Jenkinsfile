#!/usr/bin/groovy
@Library('github.com/fabric8io/fabric8-pipeline-library@master')
def utils = new io.fabric8.Utils()
clientsNode {
  def envStage = utils.environmentNamespace('stage')
  def envProd = utils.environmentNamespace('run')
  def newVersion = ''

  checkout scm

  stage('Build Release')
  echo 'NOTE: running pipelines for the first time will take longer as build and base docker images are pulled onto the node'
  if (!fileExists ('Dockerfile')) {
    writeFile file: 'Dockerfile', text: 'FROM node:5.3-onbuild'
  }

  newVersion = getDeploymentResources {}

  def rc = getDeploymentResources {
    port = 8080
    label = 'node'
    icon = 'https://cdn.rawgit.com/fabric8io/fabric8/dc05040/website/src/images/logos/nodejs.svg'
    version = newVersion
    imageName = clusterImageName
  }

  stage('Rollout to Stage')
  kubernetesApply(file: rc, environment: envStage)

  stage('Approve')
  approve{
    room = null
    version = canaryVersion
    console = fabric8Console
    environment = envStage
  }

  stage('Rollout to Run')
  kubernetesApply(file: rc, environment: envProd)

}

/*node("launchpad-nodejs") {
  checkout scm
  stage("Build") {
    sh "npm install"
  }
  stage("Deploy") {
    sh "npm run openshift"
  }
}*/
