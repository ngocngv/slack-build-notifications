#!groovy

import groovy.json.JsonOutput
import java.util.Optional
import hudson.tasks.test.AbstractTestResultAction
import hudson.model.Actionable
import hudson.tasks.junit.CaseResult

def speedUp = '--configure-on-demand --daemon --parallel'
def nebulaReleaseScope = (env.GIT_BRANCH == 'origin/master') ? '' : "-Prelease.scope=patch"
def nebulaRelease = "-x prepare -x release snapshot ${nebulaReleaseScope}"
def gradleDefaultSwitches = "${speedUp} ${nebulaRelease}"
def gradleAdditionalTestTargets = "integrationTest"
def gradleAdditionalSwitches = "shadowJar"
def slackNotificationChannel = "builds"
def author = ""
def message = ""
def testSummary = ""
def total = 0
def failed = 0
def skipped = 0

def isPublishingBranch = { ->
    return env.GIT_BRANCH == 'origin/master' || env.GIT_BRANCH =~ /release.+/
}

def isResultGoodForPublishing = { ->
    return currentBuild.result == null
}

def notifySlack(text, channel, attachments) {
    def slackURL = 'https://hooks.slack.com/services/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'
    def jenkinsIcon = 'https://wiki.jenkins-ci.org/download/attachments/2916393/logo.png'

    def payload = JsonOutput.toJson([text: text,
        channel: channel,
        username: "Jenkins",
        icon_url: jenkinsIcon,
        attachments: attachments
    ])

    sh "curl -X POST --data-urlencode \'payload=${payload}\' ${slackURL}"
}

node {
    stage("Post to Slack") {
        notifySlack("Success!", slackNotificationChannel, [])
    }
}
