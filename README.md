# action-verify-labels

<h4 align="center"><b>Verify whether labels in the pull requests are assigned or not</b></h4>

&nbsp;
<div align="center">

<a href="https://github.com/mauroalderete/action-verify-labels/blob/main/LICENSE">
	<img alt="License: MIT" src="https://img.shields.io/badge/License-MIT-yellow.svg">
</a>
<a href="https://github.com/mauroalderete/action-verify-labels/blob/main/CODE_OF_CONDUCT.md">
	<img alt="Contributor covenant: 2.1" src="https://img.shields.io/badge/Contributor%20Covenant-2.1-4baaaa.svg">
</a>
<a href="https://semver.org/">
	<img alt="Semantic Versioning: 2.0.0" src="https://img.shields.io/badge/Semantic--Versioning-2.0.0-a05f79?logo=semantic-release&logoColor=f97ff0">
</a>

<a href="https://github.com/mauroalderete/action-verify-labels/issues/new/choose">Report Bug</a>
·
<a href="https://github.com/mauroalderete/action-verify-labels/issues/new/choose">Request Feature</a>

<a href="https://twitter.com/intent/tweet?text=👋%20Check%20this%20amazing%20repo%20https://github.com/mauroalderete/action-verify-labels,%20created%20by%20@_mauroalderete%0A%0A%23DEVCommunity%20%23100DaysOfCode%20%23Github%20%23githubactions%20✌️">
	<img src="https://img.shields.io/twitter/url?label=Share%20on%20Twitter&style=social&url=https%3A%2F%2Fgithub.com%2Fatapas%2Fmodel-repo">
</a>
</div>

&nbsp;
# :wave: Introducing `action-verify-labels`
`action-verify-labels` is a composite GitHub action to check that a pull request has the correct labels assigned.

- Do you want to block a merge if the pull request hasn't a specific label?
- Do you wish to launch a request review if the pull request has an extra label or it lacks others?
- Do you need to check if a pull request with contradictory labels is assigned?

`action-verify-labels` help you the simple way to handle the pull-request flow.

Check the labels before merging is especially useful when you use [Automatically generated release notes](https://docs.github.com/en/repositories/releasing-projects-on-github/automatically-generated-release-notes) feature. It allows controlling that to be showing the correct merge summary in the release notes.

Furthermore, `action-verify-labels` can help you to protect your branches of the pull request that don't must be merged, combining labels and [rules branch](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/defining-the-mergeability-of-pull-requests/managing-a-branch-protection-rule) feature.

&nbsp;
# :fire: Features

## Match labels with four quantifiers

You can use ```all```, ```none```, ```some```, ```just-one``` inputs to configure the labels that you want to verify in a pull request.

## Ending with error or successful customisable

Using ```emit-error-if-verify-fail``` input you can choose if `action-verify-labels` end with execution error or successful according to need.
It allows you to available a simple way to handle workflow running.

## Send request review

You can enable the sending a request review to developers participant of the pull request when the verification is passed or not.

## Status API

`action-verify-labels` provide the ```action-status``` and ```action-message``` outputs to notify you about any error event during the execution. In this way, you can take a better decision.

## Summary

`action-verify-labels` generate a friendly summary to that you can see speedily the operation result, pull request associated and end status.

&nbsp;
# :computer: How to use `action-verify-labels`

Simply add a checkout to your repository. Then, add a new step in your workflow using the `mauroalderete/action-verify-labels` action. And ready, you can start to configure it with the labels list that you wish to verify. [See examples](#examples)

## Inputs

| Input                     | Required | Default | Description                                                                                                                                                                                                                                                                                   |
| :------------------------ | :------: | :-----: | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| all                       |  false   |   ''    | List of the labels. The pull request must be assigned with all of these labels listed.                                                                                                                                                                                                        |
| none                      |  false   |   ''    | List of the labels. The pull request must not be assigned with any of these labels listed.                                                                                                                                                                                                    |
| some                      |  false   |   ''    | List of the labels. The pull request must be assigned with one or more of these labels listed.                                                                                                                                                                                                |
| just-one                  |  false   |   ''    | List of the labels. The pull request must be assigned only one of these labels listed.                                                                                                                                                                                                        |
| emit-error-if-verify-fail |  false   |  true   | The verification end with an error if the pull request not satisfy the verification. If this is enabled and the process end without any issue, then the approved == 'false', action-status == 'END' and action-message contains a description of the reason for the fail. By default is true. |
| request-review            |  false   |  false  | Boolean flag. False by default. Should send a request review to notify whether the pull request passed or not this verification.                                                                                                                                                              |
| request-review-header     |  false   |   ''    | A string that is attached to the beginning of the review comment. Supports GitHub Flavored Markdown.                                                                                                                                                                                          |
| github-token              |  false   |   ''    | The Github token. Only is required if request-review == true.                                                                                                                                                                                                                                 |
| pull-request-number       |  false   |   ''    | The pull request number to verify. Only is required if this action isn't run on a pull_request event or pull_request_target event.                                                                                                                                                            |

## Outputs

| Output         | Description                                                                   |
| :------------- | :---------------------------------------------------------------------------- |
| approved       | Boolean that indicates if the pull request approved this verification or not. |
| action-status  | Execution status of the action. [See Action status](#action-status)           |
| action-message | Message associated to the current status of the action.                       |

## Examples

Here are some common examples:

### Verify some labels and deny other

```yaml
name: Labels Test

on:
  pull_request:
    branches: [main]
    types:
      [opened, reopened, labeled, unlabeled]

jobs:
  verify-labels:
    runs-on: ubuntu-latest
    name: Verify labels
    if: github.event.pull_request.merged == false
    steps:
      - uses: actions/checkout@v3

      - name: Verify labels
        id: action-verify-labels
        uses: mauroalderete/action-verify-labels@v1
        with:
          none: question, wontfix, invalid
          some: bug, feature, BREAKING CHANGE
```
This workflow is on when a new pull request is opened or reopened, and it is on when some label assignment changes too.

The `action-verify-labels` get the pull request reference from GitHub context and search that to be hadn't assigned ```question```, ```wontifx```, ```invalid``` labels; and verify that at least has assigned one of ```bug```, ```feature```, ```BREAKING CHANGE``` labels.

If the verification doesn't fulfil then the action end with an execution error. This behaviour can be useful if you need to block other operations or if you are using badges. Yet, this behaviour can be changed.

### Verify labels and exit without execution error when the verification fails.

```yaml
      - name: Verify labels
        id: action-verify-labels
        uses: mauroalderete/action-verify-labels@v1
        with:
          none: question, wontfix, invalid
          some: bug, feature, BREAKING CHANGE
          emit-error-if-verify-fail: false
```

We have the same case that example before. But now if the pull request doesn't fulfil the labels rule configured the action end is always successful. It's to say, it doesn't emit an error.

It config is useful when only want to check but not break the running the workflow.

> How alternative to manage the running workflow when a step exits with the error you may use [continue-on-error](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_idstepscontinue-on-error). However, depending of situation the input ```emit-error-if-verify-fail: false``` can result in you most simple of the implement.

If the action doesn't end with an error when a pull request doesn't pass, how you can know if the pull request is verified or not? We can use the status output.

### Check the status output to bifurcate the flow

```yaml
    steps:
      - uses: actions/checkout@v3

      - name: Verify labels
        id: action-verify-labels
        uses: mauroalderete/action-verify-labels@v1
        with:
          none: question, wontfix, invalid
          some: bug, feature, BREAKING CHANGE
          emit-error-if-verify-fail: false

      - name: Do stuff when PR was verified
        if: ${{ steps.action-verify-labels.action-status }} == 'END' && ${{ steps.action-verify-labels.approved }} == 'true'
        run: echo "PR is ok!!!"

      - name: Do stuff when PR wasn't verified
        if: ${{ steps.action-verify-labels.action-status }} == 'END' && ${{ steps.action-verify-labels.approved }} == 'false'
        run: echo "PR has wrong labels assigned"
```

We can use the ```action-status``` and ```approved``` outputs to check if the pull request pass or not the labels verify. In this example, we can see two steps that will be executed according to the result of the verification.

#### Action status

There are many statuses possible. Each one often comes accompanied by a description message. The status reveals the last operation that `action-verify-labels` was executing before it was interrupted. If the ```action-status``` is `END` means the `action-verify-labels` ended correctly.

| action-status           | description                                                                      |
| :---------------------- | :------------------------------------------------------------------------------- |
| VALIDATION              | Some input contains a bad format o lacks some input                              |
| GET_LABELS_FROM_CONTEXT | Was a problem to request GitHub info about the PR                                |
| VERIFY_ALL_LABELS       | Fail to parse labels listed in ```all``` input                                   |
| VERIFY_NONE_LABELS      | Fail to parse labels listed in ```none``` input                                  |
| VERIFY_SOME_LABELS      | Fail to parse labels listed in ```some``` input                                  |
| VERIFY_JUST_ONE_LABELS  | Fail to parse labels listed in ```just-one``` input                              |
| GET_LAST_REVIEW         | Something was wrong when to try to get the last request review sent on PR        |
| SEND_APPROVED_REVIEW    | Something was wrong when to try to upload an APPROVE request review to PR        |
| SEND_UNAPPROVED_REVIEW  | Something was wrong when to try to upload a REQUEST_CHANGES request review to PR |
| END                     | the action finished successfully, whether verification was passed or not         |


### Send request reviews to developers
```yaml
      - name: Verify labels
        id: action-verify-labels
        uses: mauroalderete/action-verify-labels@v1
        with:
          none: question, wontfix, invalid
          some: bug, feature, BREAKING CHANGE
          request-review: true
          request-review-header: '**:bookmark: verify-labels-action**'
          github-token: ${{ secrets.GITHUB_TOKEN }}
```

`action-verify-labels` allow uploading a request review when a pull request is passed or not verified. In this way, the developers can see speedily if needed to make some changes.

The action sends two review kinds:

- APPROVED, when a pull request passed the verify and the last review sent by this action was CHANGES_REQUESTED (or it hasn't been any review sent by this action).
- CHANGES_REQUESTED, when a pull request doesn't pass the verification and the last review sent by this action was APPROVED (or it hasn't been any review sent by this action).

This feature is to be enabled with set input ```request-review: true```. When we use sending request review the ```github-token``` is required.

Optionally, you can fill the input ```request-review-header``` with some string to show at beginning of the review body message. It is useful for differentiate between others request reviews that can be created by other GitHub Actions.

The review header content can be any string UTF-8 and Support [GitHub Flavored Markdown](https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax).

> Additional, ```request-review-header``` value is used by `action-verify-labels` to identify the last review sent. I recommend completing it if you use other actions that create request revisions.

> Please, feel free to write any question, suggestion or issue using creating a [`new issue`](https://github.com/mauroalderete/action-verify-labels/issues/new/choose).

&nbsp;
# :building_construction: How to Set up `action-verify-labels` for Development?

You set up `action-verify-labels` locally with a few easy steps.

1. Clone the repository

```bash
git clone https://github.com/mauroalderete/action-verify-labels
```

2. Change the working directory

```bash
cd action-verify-labels
```

&nbsp;
# :hamburger: How to work

The action runs on ubuntu runner as composite action, so that it has available all cli tools ready for use.

The objective of this project is to reduce the cost of time to resolve the verification. For this reason, the `action-verify-labels` is built as composite. It allows us to reduce the number of dependencies to must be loaded in real-time to run the action by leveraging installed tools in the GitHub-hosted runner.

`action-verify-labels` run multiples bash commands together to bundle a unified action. The commands are grouped in many steps, each one represents a conceptual action state and it focuses on a simple, single function.

To handle the execution flow it contains a simple pattern to trace the action state.
It uses this state to generate the summary at the end and determine if it needs to exit with an execution error or not.

To get the context and make a request, it uses the GitHub-CLI subcommand. It uses many environment variables to share information between steps too. Instead, the parse labels list and math use a combination of jq, awk and sed commands.

We can find many lines commented that explain it that is doing and that variables affect the process.

If you have any questions, felt you free to make a comment through a [issue](https://github.com/mauroalderete/action-verify-labels/issues/new/choose).

If you want to improve `action-verify-labels` o simply report a bug, please check the [contributing](#handshake-contributing-to-action-verify-labels) section to know how to do it.

&nbsp;
# :shield: License

This project is licensed under the MIT License - see the [`LICENSE`](LICENSE) file for details.

&nbsp;
# :handshake: Contributing to `action-verify-labels`

Any kind of positive contribution is welcome! Please help us to grow by contributing to the project.

If you wish to contribute, you can work on any [issue](https://github.com/mauroalderete/action-verify-labels/issues/new/choose) or create one on your own. After adding your code, please send us a Pull Request.

> Please read [`CONTRIBUTING`](CONTRIBUTING.md) for details on our [`CODE OF CONDUCT`](CODE_OF_CONDUCT.md), and the process for submitting pull requests to us.

&nbsp;
# :pray: Support

We all need support and motivation. `action-verify-labels` is not an exception. Please give this project a :star: start to encourage and show that you liked it. Don't forget to leave a :star: star before you move away.

If you found the app helpful, consider supporting us with a coffee.

&nbsp;
<div align="center">
<a href='https://cafecito.app/mauroalderete' rel='noopener' target='_blank'><img srcset='https://cdn.cafecito.app/imgs/buttons/button_6.png 1x, https://cdn.cafecito.app/imgs/buttons/button_6_2x.png 2x, https://cdn.cafecito.app/imgs/buttons/button_6_3.75x.png 3.75x' src='https://cdn.cafecito.app/imgs/buttons/button_6.png' alt='Invitame un café en cafecito.app' /></a>
</div>
