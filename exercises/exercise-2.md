# Exercise 2 - Script injection in github-script action
## :bomb: Exploiting the script injection vulnerability in github-script action
The [Check issue comment workflow](.github/workflows/check-issue-comment.yml) uses the issue comment body in the [github-script actions](https://github.com/actions/github-script) as follows:
```
const comment="${{ github.event.comment.body }}"
```
Because this value is provided by the user when they create an issue commit, this provides an opportunity for an attacker to exploit the workflow with by crafting an issue comment that tricks the script into executing code where it's expecting data.

If we create an issue comment with the contents...
```
octocat";console.log('WTF!!!');//
```
...the script looks like the following...
```
const comment="octocat";console.log('WTF!!!');//"
```
This payload closes the initial quote, then ends the command with a semicolon. This allows the following `console.log('WTF!!!');` command to run as a separate command.  

### Create an issue comment with the exploit payload
Let's see this in action by creating a new issue comment with this body and see what happens.  
Follow the steps below to exlpoit the script injection vulnerability in your repo:  
1. Go to the Issues tab and click one of the issue we had created previously.
2. In the comment field at the bottom, enter `console.log('WTF!!!');//` and click the green `Comment` button.
3. Navigate to the Actions tab and select the `Check issue comment` actions workflow on the left.  
4. Click on the workflow run.  
5. Click on the job `check-issue-comment`.  
6. In the `check-issue-comment` job lob, click the `>` to the left of `Check issue comment` step to see the output.  
7. Observe that the workflow runs the command `console.log('WTF!!!');//`!  

<img width="1034" alt="Screenshot 2023-08-30 at 9 07 51 PM" src="https://github.com/robandpdx/workflow-script-injection/assets/95243761/8730dc36-b596-4766-b24a-ec85209a6763">

## :lock: Fixing the script injection vulnerability in github-script action
Similar to the run command, the github-script action will create a script from its input and run that script. The github-script action does not know the difference between commands and data. Therefore, when user input is put directly into the github-script action, there is a script injection vulnerability that can be exploited as we just did.  

The way to mitigate this vulnerability is to put the user input into an environment variable, which is not used to generate the script that the github-script action executes.  

1. Open the file [.github/workflows/check-issue-comment.yml](.github/workflows/check-issue-comment.yml)  
2. Add an environment variable section to the `Check issue comment` step...
```
      - name: Check issue comment
        uses: actions/github-script@v6
        env:
            ISSUE_TITLE: ${{ github.event.issue.title }}
```
3. Replace the user input with the new environment variable in the script by removin this line...
```
          const comment="${{ github.event.comment.body }}"
```
and replacing it with this one...
```
          const comment=process.env.ISSUE_COMMENT
```

4. Test this out by [creating a new issue comment as we did above](#create-an-issue-comment-with-the-exploit-payload).  
6. Notice that the `console.log('WTF!!!');//` command does not appear in the output as it did previously.  