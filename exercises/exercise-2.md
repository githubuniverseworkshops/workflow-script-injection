# Exercise 2 - :bomb: Exploiting the script injection vulnerability in github-script action
The [Check issue comment workflow](.github/workflows/check-issue-comment.yml) uses the issue comment body in the [github-script actions](https://github.com/actions/github-script) as follows:
```
const comment="${{ github.event.comment.body }}"
```
Because this value is provided by the user when they create an issue commit, this provides an opportunity for an attacker to exploit the workflow with by crafting an issue comment that tricks the script into executing code where it's expecting data.

If we create an issue comment with the contents...
```
octocat";console.log('Script injected!!!');//
```
...the script looks like the following...
```
const comment="octocat";console.log('Script injected!!!');//"
```
This payload closes the initial quote, then ends the command with a semicolon. This allows the following `console.log('Script injected!!!');` command to run as a separate command.  

### Step by step
Let's see this in action by creating a new issue comment with this body and see what happens.  
Follow the steps below to exlpoit the script injection vulnerability in your repo:  
1. Go to the Issues tab and click one of the issue we had created previously.
2. In the comment field at the bottom, enter `octocat";console.log('Script injected!!!');//` and click the green `Comment` button.
3. Navigate to the Actions tab and select the `Check issue comment` actions workflow on the left.  
4. Click on the workflow run.  
5. Click on the job `check-issue-comment`.  
6. In the `check-issue-comment` job lob, click the `>` to the left of `Check issue comment` step to see the output.  
7. Observe that the workflow runs the command `console.log('Script injected!!!');//`!  

![alt script injection output in log](/images/exercise2.png "script injection output in log")