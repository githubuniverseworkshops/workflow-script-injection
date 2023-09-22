# Exercise 3 - Mitigate script injection in the run command
To mitigate the script injection issue in the [Check issue title](.github/workflows/check-issue-title.yml) workflow that we saw in exercise 1, we need to do the following:
1. Remove `${{ github.event.issue.title }}` from the run command 
2. Set and environment variable to the value `${{ github.event.issue.title }}`
3. Use the environment variable in the run command

The `Check issue title` step should look like this after our edits...
```
      - name: Check issue title
        env:
            ISSUE_TITLE: ${{ github.event.issue.title }}
        run: |
        if [[ $ISSUE_TITLE =~ ^octocat ]]; then
        echo "Issue title starts with 'octocat'"
        exit 0
        else
        echo "Issue title did not start with 'octocat'"
        exit 1
        fi
```

Now let's create a new issue with the title we used to exploit the script injection vulnerability we saw in exercise 1 to see if we have mitigated the issue. Create a new issue titled `octocat"; ls -l $GITHUB_WORKSPACE"`.

<img width="1002" alt="Screenshot 2023-09-11 at 5 24 09 PM" src="https://github.com/robandpdx/workflow-script-injection/assets/95243761/c3026073-eb65-443e-96e7-9ceb560fd5eb">

We find that the workflow does not execute the `ls -l $GITHUB_WORKSPACE` command. Success!