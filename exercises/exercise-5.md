# Exercise 5 - :lock: Fixing the script injection vulnerability in github-script action
Similar to the run command, the github-script action will create a script from its input and run that script. The github-script action does not know the difference between commands and data. Therefore, when user input is put directly into the github-script action, there is a script injection vulnerability that can be exploited as we just did.  

The way to mitigate this vulnerability is to put the user input into an environment variable, which is not used to generate the script that the github-script action executes.  

### Step by step
1. Open the file [.github/workflows/check-issue-comment.yml](.github/workflows/check-issue-comment.yml)  
2. Add an environment variable section to the `Check issue comment` step...
```
      - name: Check issue comment
        uses: actions/github-script@v6
        env:
            ISSUE_TITLE: ${{ github.event.issue.title }}
        with:
          script:
          ...
```
3. Replace the user input with the new environment variable in the script by removing this line...
```
      with:
        script:
          const comment="${{ github.event.comment.body }}"
          ...
```
...and replacing it with this one...
```
      with:
        script:
          const comment=process.env.ISSUE_COMMENT
          ...
```

4. Test this out by [creating a new issue comment as we did in exercise 2](./exercise-2.md#create-an-issue-comment-with-the-exploit-payload).  
6. Notice that the `console.log('Script injected!!!');//` command does not appear in the output as it did previously.  
