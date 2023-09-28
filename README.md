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

  - Understand Script Injection vulnerabilities in GitHub Actions Workflows
  - Learn how CodeQL can detect Script Injection in GitHub Actions Workflows
  - Understand how GitHub Advanced Security can mitigate Script Injection vulnerabilities
  - Learn how to enhance the detection of vulnerabilties in Workflows using third-party queries
  - Learn how to fix the Script Injection vulnerabilties in GitHub Actions Workflows
  - Learn how to use the Codespace for CodeQL to develop custom queries
  - Learn how to create custom queries to further enhance the protection of GitHub Actions Workflows 

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
```
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
```
The [Check issue title workflow](.github/workflows/check-issue-title.yml) simply checks if the title (github.event.issue.title) of the workflow begins with `octocat`. If so, the workflow succeeds. If not, the workflow fails.  

The [Check issue title with action workflow](.github/workflows/check-issue-title-with-action.yml) uses an [action](.github/actions/check-issue-title-action/action.yml) that simply checks if the input of the action begins with `octocat`. If so, the action succeeds. If not, the action fails.  

This workflow and action abore are vulnerable to script injection. Let's find out why they are vulnerable, and how to exploit them.
[Exercise 1](./exercises/exercise-1.md)  

## :bomb: Exercise 2 - Script injection in github-script action

The [Check issue comment](.github/workflows/check-issue-comment.yml) workflow simply checks if the issue comment (github.event.comment.body) begins with `octocat`. If so, the workflow succeeds. If not, the workflow fails.  

This workflow is vulnerable to script injection. Let's find out why it is vulnerable, and how to exploit it.  
[Exercise 2](./exercises/exercise-2.md)  

## :mag: Exercise 3 - Mitigate using CodeQL Action Workflow

In CodeQL, code is treated like data. Security vulnerabilities, bugs, and other errors are modeled as queries that can be executed against databases extracted from code. You can run the standard CodeQL queries, written by GitHub researchers and community contributors, or write your own to use in custom analyses. Queries that find potential bugs highlight the result directly in the source file.

GitHub Advanced Security uses CodeQL as the tool for Code Scanning. Code Scanning creates security alerts when vulnerabilties are found. These alerts can be viewed in GitHub and can block merges in protected branches. When a developer fixes a vulnerability, GitHub willl automatically close the alert as resolved.

In the next exercise we will learn how CodeQL can detect Script Injection in GitHub Actions Workflows and understand how GitHub Advanced Security can mitigate Script Injection vulnerabilities.

[Exercise 3](./exercises/exercise-3.md)  

## :european_castle: Exercise 4 - Enhance the detection of vulnerabilities using third party queries
We know CodeQL is a perfect tool for detecting vulnerablities because:
- It helps us to treat Workflows as code
- Treat code as data and extract it into a database
- Look for known vulnerabilities using built-in queries
- Create custom queries and expand coverage;
- Use code scanning to create alerts
- Use actions to block PRs
- Use deployment protection rules to block jobs

In the next exercise we will explore how to expand the coverage by using third party queries to detect `unpinned` actions.
 
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
