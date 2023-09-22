# Exercise 2 - Script injection in github-script action
The [Check issue comment](.github/workflows/check-issue-comment.yml) workflow uses issue comment body in the [github-script actions](https://github.com/actions/github-script) as follows:
```
const comment="${{ github.event.comment.body }}"
```
This provides an opportunity for an attacker to exploit the workflow with an issue comment `octocat";console.log('WTF!!!');//`. Using this title, the script looks like the following...
```
const comment="octocat";console.log('WTF!!!');//"
```

Let's create a new issue comment with this body and see what happens. We observe that the workflow runs the command `console.log('WTF!!!');//`!  

<img width="1034" alt="Screenshot 2023-08-30 at 9 07 51 PM" src="https://github.com/robandpdx/workflow-script-injection/assets/95243761/8730dc36-b596-4766-b24a-ec85209a6763">