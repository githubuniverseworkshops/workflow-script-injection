# Exercise 4 - :lock: Fixing the script injection vulnerability in the run command
The run command will create a script from its input and run that script. The run command does not know the difference between commands and data. Therefore, when user input is put directly into the run command, there is a script injection vulnerability that can be exploited as we just did.  

The way to mitigate this vulnerability is to put the user input into an environment variable, which is not used to generate the script that the run command executes.  

### Step by step
Let's edit the [Check issue title workflow](.github/workflows/check-issue-title.yml) to use an environment variable. Putting the user input into an environment variable, then using the environment variable in the script will mitigate the script injection vulnerability.  

1. Open the file [.github/workflows/check-issue-title.yml](.github/workflows/check-issue-title.yml)  
2. Add an environment variable section to the `Check issue title` step...
```
      - name: Check issue title
        env:
            ISSUE_TITLE: ${{ github.event.issue.title }}
        run: |
        ...
```
3. Use the new environment variable in the run command...
```
        run: |
          title="$ISSUE_TITLE"
          ...
```
4. Test this out by [creating a new issue as we did in exercise 1](./exercise-1.md#create-an-issue-with-the-exploit-payload).
5. Notice that the `ls -s $GITHUB_WORKSPACE` command does not appear in the output as it did previously.  

Next, let's edit the [Check issue title action](.github/actions/check-issue-title-action/action.yml) to use an environment variable also.
1. Open the file [.github/actions/check-issue-title-action/action.yml](.github/actions/check-issue-title-action/action.yml)  
2. Add an environment variable section to the `Check issue title` step...
```
      - name: Check issue title
        shell: bash
        env:
            ISSUE_TITLE: ${{ github.event.issue.title }}
        run: |
        ...
```
3. Use the new environment variable in the run command...
```
        run: |
          if [[ "$ISSUE_TITLE" =~ ^octocat ]]; then
          ...
```
