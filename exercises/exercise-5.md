# Exercise 5 - :lock: Fixing the script injection vulnerability in the run command
The run command will create a script from its input and run that script. The run command does not know the difference between commands and data. Therefore, when user input is put directly into the run command, there is a script injection vulnerability that can be exploited as we just did.  

The way to mitigate this vulnerability is to put the user input into an environment variable, which is not used to generate the script that the run command executes.  

Let's edit the [Check issue title workflow](.github/workflows/check-issue-title.yml) to use an environment variable that we set using the user input.

1. Open the file [.github/workflows/check-issue-title.yml](.github/workflows/check-issue-title.yml)  
2. Add an environment variable section to the `Check issue title` step...
```
      - name: Check issue title
        env:
            ISSUE_TITLE: ${{ github.event.issue.title }}
```
3. Use the new environment variable in the run command...
```
          title="$ISSUE_TITLE"
```
4. Test this out by [creating a new issue as we did above](#create-an-issue-with-the-exploit-payload).  
5. Notice that the `ls -s $GITHUB_WORKSPACE` command does not appear in the output as it did previously.  
