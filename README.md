<h1 align="center">Understanding the risk of script injection in GitHub Actions workflows</h1>
<h5 align="center">@robandpdx</h5>
<h5 align="center">@decyjphr</h5>

<p align="center">
  <a href="#mega-prerequisites">Prerequisites</a> •  
  <a href="#books-resources">Resources</a> •
  <a href="#learning-objectives">Learning Objectives</a>
</p>

In this workshop we will learn about the risk of script injection in GitHub Actions workflows, and how to mitigate that risk.  

- **Who is this for**: developers, devops engineers
- **What you'll learn**: the risk of script Injections in GitHub Actions workflows, and how to migirate that risk
- **What you'll build**: workflows that are not vulnerable to script injection attacks

## Learning Objectives

In this workshop, you will:

  - learn about script injection vulnerabilities in GitHub actions workflows
  - learn how to mitigate script injection vulnerabilities in GitHub actions workflows
  - learn how Github Advanced Security can help you build secure GitHub actions workfows

## :mega: Prerequisites
Before joining the workshop, there are a few items that you will need to install or bring with you.
- an account on GitHub.com
- a public repo created from this template repo

## :bomb: Exercise 1: Script injection in the run command

There are many issue ops things you can do with GitHub actions. A common use case is request/approval process:
1. User opens an issue requesting X.
2. If conditions are met, approval is automatic, and a GitHub actions workflow fulfills the request.

An example of this is [request-repo-create](https://github.com/robandpdx/request-repo-create).  

In this example, user input from the issue body is parsed and used in the workflow as the name of the new repository to be created.  

You should always be careful when using user inputs in your worfklows. User input can come from any of the following sources:

github.event.issue.title  
github.event.issue.body  
github.event.pull_request.title  
github.event.pull_request.body  
github.event.comment.body  
github.event.review.body  
github.event.review_comment.body  
github.event.pages.*.page_name  
github.event.commits.*.message  
github.event.head_commit.message  
github.event.head_commit.author.email  
github.event.head_commit.author.name  
github.event.commits.*.author.email  
github.event.commits.*.author.name  
github.event.pull_request.head.ref  
github.event.pull_request.head.label  
github.event.pull_request.head.repo.default_branch  
github.head_ref  

The [Check issue title workflow](.github/workflows/check-issue-title.yml) simply checks if the title (github.event.issue.title) of the workflow begins with `octocat`. If so, the workflow succeeds. If not, the workflow fails.  

The [Check issue title with action workflow](.github/workflows/check-issue-title-with-action.yml) uses an [action](.github/actions/check-issue-title-action/action.yml) that simply checks if the input of the action begins with `octocat`. If so, the action succeeds. If not, the action fails.  

This workflow and action abore are vulnerable to script injection. Let's find out why they are vulnerable, and how to exploit them.
[Exercise 1](./exercises/exercise-1.md)  

## :bomb: Exercise 2 - Script injection in github-script action

The [Check issue comment](.github/workflows/check-issue-comment.yml) workflow simply checks if the issue comment (github.event.comment.body) begins with `octocat`. If so, the workflow succeeds. If not, the workflow fails.  

This workflow is vulnerable to script injection. Let's find out why it is vulnerable, and how to exploit it.  
[Exercise 2](./exercises/exercise-2.md)  

## :mag: Exercise 3 - Mitigate using CodeQL Action Workflow

Let's create an actions workflow to scan our workflow files using CodeQL.  
[Exercise 3](./exercises/exercise-3.md)  

## :european_castle: Exercise 4 - Enhance the detection of vulnerabilities using third party queries

Now let's look at another way we can use CodeQL to secure our GitHub actions workflows.  
[Exercise 4](./exercises/exercise-4.md)  

## :lock: Exercise 5 - Fixing the script injection vulnerability in the run command

Now let's learn how to mitigate the script injection vulnerability in the run command.  
[Exercise 5](./exercises/exercise-5.md)

## :lock: Exercise 6 - Fixing the script injection vulnerability in github-script action

Now let's learn how to mitigate the script injection vulnerability in the github-script action.  
[Exercise 6](./exercises/exercise-6.md)

## :books: Resources
- [Understanding the risk of script injections](https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions#understanding-the-risk-of-script-injections)
- [Security hardening for GitHub Actions](https://docs.github.com/en/enterprise-cloud@latest/actions/security-guides/security-hardening-for-github-actions)  
- [CodeQL queries](https://github.com/advanced-security/codeql-queries/)  