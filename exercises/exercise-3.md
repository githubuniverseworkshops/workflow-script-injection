# Exercise 3 - Detect Script Injection using CodeQL
### :keyboard: Activity: Creating an Actions workflow to scan Workflow files using CodeQL
In this section we are going to create an Actions workflow to scan existing workflows for any `script injection` weaknesses.

1. In your repository, `click` on the [`Actions`](../../../actions) tab.

2. This will take you to the `Actions` page and now click on the [`new workflow`](../../../actions/new) button to create a workflow.

3. This will put you in the `starter workflows` page. Enter `CodeQL Analysis` in the `Search` field and search. 
You should see one result. Click on `Configure` button on the resulting workflow template. This will take you to the edit window of the the workflow file.

Now we can edit this workflow to customize it to scan the workflows.

4. Give a name to the file (it could be `actions-workflow-codeql.yml`) and also give a name to the workflow (this could be `Actions WorkFlow CodeQL`)

At this point, you are close to having a CodeQL Workflow that can scan your repository for vulnerabitlities. 

5. Edit the workflow file as follows:
   
Look over the first few lines of the workflow. You'd notice that the workflow gets triggered by `push` to the `default` branch and also by several other events.
Edit the workflow's trigger section as follows:
  - Keep the `push` trigger
  - Remove other triggers that were pre-configured in the workflow. 
  - In the `strategy`:`matrix`: `language` section, type `'javascript'` as the value for lanuage array.
  - Remove the `Autobuild` step entirely.  
  
  > **NOTE**  
  > Autobuild is only necessary for compiled languages, since we are using the `javascript` extractor, this is not really necessary.

6. Commit this file into the `default`branch.
When the file is committed, it will generate a `push` event and the `Actions WorkFlow CodeQL` workflow should be triggered. Now `click` on the [`Actions`](../../actions) tab and you should see the workflow being scheduled to run based on the `push` event. 

7. Monitor the workflow run and ensure that it finishes successfully.

8. Now, click on the `Security` tab. And you should see the `Security Overview` page with _**two**_ alerts created under `Code Scanning` .

### :keyboard: Activity: Analysing and fixing the Alert
9. Click on `Code Scanning` in the side menubar of the `Security Overview` page. And click on the first alert - `Expression Injection in Actions`

You'll see the details of the alert including the file where this weakness exists.

10. Click on `Show more` to see more details including how to resolve this alert.

The [Exercise 5](../exercises/exercise-5.md) and [Exercise 6](../exercises/exercise-6.md) will show how to modify the problematic workflow file to resolve this alert. At the end of those exercises, once the file is committed, it will trigger the `Actions Workflow CodeQL` and the alert should be resolved if the recommend fix was implemented.

> **NOTE**  
> If `CodeQl Analysis` search is not returning any results, code scanning might not be enabled for the repo, please contact your organization admin or repository admin to enable it. If you want to learn more about setting up code scanning, you can follow [this tutorial](https://learn.microsoft.com/en-us/training/modules/configure-code-scanning/2-what-code-scanning).

> **NOTE**    
> For your convenience a sample of this workflow is available in [`/solutions`](/solutions) directory.

### 🎥 Demo
https://github.com/decyjphr-demo/demo/assets/57544838/139b1512-143b-463f-9871-50ede2f2a4fb
