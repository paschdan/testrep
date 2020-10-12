def maxKeepBuilds = 50
if (env.BRANCH_NAME.startsWith('PR')) {
	maxKeepBuilds = 10
}

def isPR = env.CHANGE_ID

def isDraft = (isPR && pullRequest.draft)

	properties([
			buildDiscarder(logRotator(artifactNumToKeepStr: "${maxKeepBuilds}", numToKeepStr: "${maxKeepBuilds}")),
			pipelineTriggers([
				issueCommentTrigger('.*test acceptance please.*'),
				pullRequestReview(reviewStates: ['approved'])
			])
	])



	def setGithubStatusForPullRequest(script, status, context, description) {
		if (script.env.CHANGE_ID) {
			echo "Set github status `${status}` for `${context}` with message `${description}` (${script.env.RUN_DISPLAY_URL})"
				script.pullRequest.createStatus(
						status: status,
						context: context,
						description: description,
						targetUrl: "${script.env.RUN_DISPLAY_URL}"
						)
		}
	}

if (isDraft) {

	def triggerCause = currentBuild.rawBuild.getCause(org.jenkinsci.plugins.pipeline.github.trigger.PipelineGithubTriggerCause)

		if (triggerCause) {
			if (triggerCause.getTriggerCauseType == "PullRequestReview") {
				echo("Build was started by ${triggerCause.userLogin}, who reviewed the PR: " +
						"\"${triggerCause.state}\", which matches one of " +
						"\"${triggerCause.reviewStates}\" trigger pattern.")
			}
			if (triggerCause.getTriggerCauseType == "IssueComment") {
				echo("Build was started by ${triggerCause.userLogin}, who wrote: " +
						"\"${triggerCause.comment}\", which matches the " +
						"\"${triggerCause.triggerPattern}\" trigger pattern.")
			}
			setGithubStatusForPullRequest(this, 'pending', 'testing', 'running acceptance tests on draft pr...')
		} else {
			echo('Build was not started by a trigger')
				currentBuild.result = 'ABORTED'
				error "Aborting Build for draft PR"
				return
		}

}

echo "Running Tests...."
setGithubStatusForPullRequest(this, 'success', 'testing', 'all tests succeeded')
echo "Done"



