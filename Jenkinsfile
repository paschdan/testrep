def isPR = env.CHANGE_ID

def isDraft = (isPR && pullRequest.draft)

properties([
        buildDiscarder(logRotator(artifactNumToKeepStr: "${maxKeepBuilds}", numToKeepStr: "${maxKeepBuilds}")),
        pipelineTriggers([
                issueCommentTrigger('.*test acceptance please.*')
        ])
])

def setGithubStatusForPullRequest = (context, status, prcontext, desc) {

pullRequest.createStatus(status: status,
                             context: prcontext,
                             description: desc,
                             targetUrl: "$RUN_DISPLAY_URL")
}

setGithubStatusForPullRequest(this, 'pending', 'testing', 'running acceptance tests on draft pr...')


if (isDraft) {
    def triggerCause = currentBuild.rawBuild.getCause(org.jenkinsci.plugins.pipeline.github.trigger.IssueCommentCause)
    if (triggerCause) {
        setGithubStatusForPullRequest(this, 'pending', 'testing', 'running acceptance tests on draft pr...')
    } else {
        setGithubStatusForPullRequest(this, 'pending', 'testing', 'please trigger the build when the PR is ready')
        currentBuild.result = 'ABORTED'
        error "Aborting Build for draft PR"
        return
    }
}

echo "Running Tests...."
echo "Done"



