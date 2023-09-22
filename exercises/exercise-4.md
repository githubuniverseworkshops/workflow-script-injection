# Exercise 4 - Migrate script injection in github-script action
To mitigate the script injection issue in the [Check issue comment](.github/workflows/check-issue-comment.yml) workflow, we need to do the following:
1. Remove `${{ github.event.issue.title }}` from our github-script action usage
2. Set and environment variable to the value `${{ github.event.issue.title }}`
3. Use the environment variable in our github-script action usage

The `Check issue comment` step should look like this after our edits...  
```
      - name: Check issue comment
        uses: actions/github-script@v6
        env:
            ISSUE_COMMENT: ${{ github.event.comment.body }}
        with:
          script: |
            const comment=process.env.ISSUE_COMMENT
            if (comment.startsWith('octocat')) {
              console.log("Issue comment starts with 'octocat'")
            } else {
              console.log("Issue comment did not start with 'octocat'")
              process.exit(1)
            }
```

Now let's create a new issue comment with the contents we used to exploit the script injection vulnerability we saw in exercise 3 to see if we have mitigated the issue. Create a new issue comment with the contents `octocat";console.log('WTF!!!');//`.

<img width="1002" alt="Screenshot 2023-09-12 at 10 30 57 AM" src="https://github.com/robandpdx/workflow-script-injection/assets/95243761/36134189-581a-41c8-9fa2-2401f01c0881">

We find that the workflow does not execute the `console.log('WTF!!!');` code. Success!