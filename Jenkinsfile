#!/bin/groovy
import org.kohsuke.github.GHCommitState;

node {
  try {
    properties([
      buildDiscarder(logRotator(artifactNumToKeepStr: '20', numToKeepStr: '20')),
      [$class: 'GithubProjectProperty', displayName: '', projectUrlStr: 'https://github.com/vishvesh-nvidia/samplePRTest/'],
      [$class: 'org.jenkinsci.plugins.workflow.job.properties.PipelineTriggersJobProperty', triggers:[
        [
          $class: 'org.jenkinsci.plugins.ghprb.GhprbTrigger',
          orgslist: 'vishvesh-nvidia',
          cron: 'H/5 * * * *',
          triggerPhrase: 'special trigger phrase',
          onlyTriggerPhrase: true,
          useGitHubHooks: false,
          permitAll: true,
          autoCloseFailedPullRequests: false,
          displayBuildErrorsOnDownstreamBuilds: true,
          extensions: [
            [
              $class: 'org.jenkinsci.plugins.ghprb.extensions.status.GhprbSimpleStatus',
              commitStatusContext: 'Docker Build',
              showMatrixStatus: false,
              triggeredStatus: 'Starting job...',
              startedStatus: 'Building...',
              completedStatus: [
                [$class: 'org.jenkinsci.plugins.ghprb.extensions.comments.GhprbBuildResultMessage', message: 'Done', result: GHCommitState.SUCCESS],
                [$class: 'org.jenkinsci.plugins.ghprb.extensions.comments.GhprbBuildResultMessage', message: 'Failed', result: GHCommitState.FAILURE]
              ]
            ]
          ]
        ]
      ]]
    ])

    stage('Checkout') {
      checkout([$class: 'GitSCM', branches: [[name: '${sha1}']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'bg_local', name: 'origin', refspec: '+refs/pull/*:refs/remotes/origin/pr/*', url: 'git@github.com:vishvesh-nvidia/samplePRTest.git']]])
    }
  } catch (Exception ex) {
    currentBuild.result = 'FAILURE'
    echo ex.toString()

    // Throw RejectedAccessException again if its a script privilege error
    if (ex.toString().contains('RejectedAccessException')) {
      throw ex
    }
  }
}
