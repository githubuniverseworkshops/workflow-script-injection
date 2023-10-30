# Exercise 7 - Enhance the detection of vulnerabilities using custom queries

# Exercise 7a - Learn CodeQL basics

## Setup the development environment
Your repository has a configuration to start a Codespace with everything you need contained within this one codespace. If and when you’re ready you can commit and push your changes up to the repository. If you were just exploring and have no further need for this code then you can simply delete your codespace and it's gone forever.

You will use this CodeQL Codespace to try out the QL concepts and programming-language-agnostic examples and later use it to create your custom query. The Codespace includes a guided introduction to working with QL, and makes it easy to get started.

## Creating a codespace for your repository
https://docs.github.com/en/enterprise-cloud@latest/codespaces/developing-in-codespaces/creating-a-codespace-for-a-repository#creating-a-codespace-for-a-repository

Welcome to your shiny new Codespace running CodeQL! 


## Running the code tour

We've set up a code tour to help you get familiar with the basic concepts of CodeQL and show you how to run CodeQL queries. To run the code tour:

1. In your codespace, at the bottom of the File Explorer, expand **CODETOUR**.
2. Under **CODETOUR**, expand the **CodeQL tutorial**, then click **#1**.
3. In the resulting pop up in the Editor, follow along with the tutorial, and press **Next** when you're ready to move on to the next step.

Once you've finished the code tour, your CodeQL environment will be all set up and ready to use! You can continue to explore the CodeQL template we've made for you, and execute your own CodeQL queries.

# Exercise 7b - Create a custom query

## Defect: Using  AWS_ACCESS_KEY_ID and AWS_ACCESS_KEY_SECRET instead of OIDC
If your GitHub Actions workflows need to access resources from a cloud provider that supports OpenID Connect (OIDC), you can configure your workflows to authenticate directly to the cloud provider. This will let you stop storing these credentials as long-lived secrets and provide other security benefits. 

## Creating your query

> **NOTE**
> Switch to the CodeQL extension on the left by clicking the QL logo.

1. Open the Command Palette with `Cmd/Ctrl + Shift + P`, start typing "CodeQL", and click on **CodeQL: Create Query**

2. When prompted for `Language`, enter  "Javascript", 

3. When prompted for `database` enter "{{owner}}/{{repo}}" of your current repo which contains the database. This will create an "example.ql " workflow file. 

   > **NOTE**    
   > The database was created when we ran the **exercise 3 and 4 ** 

4. **Run** the query and make sure that it works

5. **Rename** the file to "improper-aws-credentials.ql"

6. Modify `metadata` section as follows:
```
/**
 * @name Improper AWS Credentials
 * @kind problem
 * @problem.severity warning
 * @id javascript/actions/improper-aws-credentials
 * @tags actions
 *       security
 *       experimental
 */
```
   > **NOTE**
   > Giving the proper values for @name, @id, @kind is important for the proper display of the `codescanning` alert in the UI

7. Enter the body of the query as follows:

```
import javascript
import semmle.javascript.Actions

from string file, YamlNode accesskey, Actions::Workflow workflow,  Actions::Job job, Actions::Step step, Actions::Uses uses, Actions::With with
 where 
 exists(step.getUses()) and
 step.getUses() = uses and
 uses.getGitHubRepository() = "aws-actions/configure-aws-credentials" and
 step.getJob() = job and
 job.getWorkflow() = workflow and
 workflow.getFileName() = file and
 with.getStep() = step and
 with.lookup("aws-access-key-id") = accesskey
 select step, "AWS_ACCESS_KEY_ID used as AWS credential instead of OIDC in Action '" + uses.getGitHubRepository() + "' in step '$@' in file " + file, step, step.toString()
```

8. **Run** the query and you show see the results as follows:

   > Image here

   > **NOTE**
   >
   > Make sure the `{{owner}}/{{repo}} Database` is selected in the Databases section. If it's not, click the **Select** button next to its name.

   

   > **NOTE**
   >
   > To run your query, right-click within the `.ql` query file, and click **CodeQL: Run Query on Selected Database**, or Open the Command Palette with `Cmd/Ctrl + Shift + P`, start typing "CodeQL", and click on **CodeQL: Run Query on Selected Database**.



9. Now that the query is working, **save** the query.


## Modify your codescanning config to include the custom query

1. Once the query changes have been pushed, you can modify the `codeql-config.yml` file to include the custom query
   ```
   queries:
     - uses: ./codeql-custom-queries-javascript/improper-aws-credentials.ql
   ```


2.  **stage** and **commit** and **push** the following files to the repository:
    - `./codeql-config.yml`
    - `./codeql-custom-queries-javascript/codeql-pack.lock.yml`
    - `./codeql-custom-queries-javascript/codeql-pack.yml`
    - `./codeql-custom-queries-javascript/improper-aws-credentials.ql`
   
When the changes are `pushed`, the `Actions WorkFlow CodeQL` workflow should be triggered. Now `click` on the [`Actions`](../../actions) tab and you should see the workflow being scheduled to run based on the `push` event. 

3. Monitor the workflow run and ensure that it finishes successfully.

4. Now, click on the `Security` tab and you should see the `Security Overview` page and a **new** alert created.


### 🎥 Demo
https://github.com/githubuniverseworkshops/workflow-script-injection/assets/57544838/703caf1c-c461-4fbd-8450-d64b25b68420

