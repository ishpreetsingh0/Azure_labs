# lab003
Building Infrastructure with Azure Pipelines
Introduction
You have been given an ARM template for a Linux VM to deploy to Azure. Using Azure DevOps, you must check the ARM template for errors (linting), and deploy the VM to the provided Azure environment.

Solution
Log in to the Azure portal using the credentials provided on the lab instructions page.

Log in to the linVM server using the credentials provided:

ssh cloud_user@<PUBLIC_IP_ADDRESS>
Create an Azure DevOps Organization
In the main search bar, search for and select Azure DevOps organizations.

Scroll down and click My Azure DevOps Organizations.

Select your country/region from the dropdown list. Click Continue.

Select Create new organization > Continue > Continue (complete the required CAPTCHA).

Set the following values to create a project:

Project name: MyFirstProject
Visibility: Private
Click + Create project.

Push Code to Azure Repos
In the linVM virtual machine, clone the repo:
git clone -b arm https://github.com/ACloudGuru-Resources/content-az400-lab-resources.git
Move to the content-az400-lab-resources directory:
cd content-az400-lab-resources
List the contents of the directory:
ls
List the current remote origin:
git remote -v
Remove the current remote origin:
git remote remove origin
Verify that it was removed successfully:
git remote -v
Create Personal Access Token
Go back to Azure DevOps. In the upper right-hand corner, click the User settings icon > Personal access tokens.

Click + New Token and set the following values:

Name: linuxvm
Scopes: Full access
Click Create.

Copy the linuxvm token and save it in a safe location.

Click Close.

At the top, click the Azure DevOps logo. Then, select your project.

From the left navigation menu, click Repos.

Copy the contents from Push an existing repository from command line.

Back in the Linux VM, paste the push commands. When prompted for a password, use your newly created access token.

Back in the Azure Repos tab, click refresh to see the application code.

Download Self-Hosted Agent
On the bottom left, click Project settings.

Scroll down the left-hand pane and under Pipelines, select Agent pools.

Select the Default pool.

Click New agent.

Select the Linux tab.

Click the Copy icon, next to Download to copy the agent URL to your clipboard. Paste it to a text file to use in the following steps. Keep this tab and popup open.

Go back to the Linux VM. Then, change directory to the root:

cd ~
Make and navigate to a new directory called Downloads:

mkdir Downloads && cd Downloads
Run wget against the agent URL you just copied:

wget <AGENT_URL>
Navigate back to the root (cd ~).

Create and navigate to another directory called myagent:

mkdir myagent && cd myagent
Unzip the package you just downloaded. You can copy this same command from the popup in Azure DevOps to get the full package and version number:

tar zxvf <PACKAGE>
Review the contents of the directory (ls). Observe all of the files and folders for the agent.

Configure the Agent
Run the configuration script:

./config.sh
Type y to accept the license agreement.

When prompted for your server URL, go back to Azure DevOps and grab the URL from your browser. It will look something like https://dev.azure.com/clouduser..., followed by some characters (i.e., copy everything up to and not including /MyFirstProject). Paste this in the terminal.

Press Enter to accept the default authentication type of PAT.

For personal access token, copy and paste the personal access token you created and saved earlier in this lab.

Press Enter to accept the default agent pool.

Press Enter to accept the default name.

Press Enter to accept the default work folder.

Run the agent interactively so that it is always listening for any jobs:

./run.sh
Create the Build Pipeline
Back in Azure DevOps, in the left-hand menu, click Pipelines.
Click Create Pipeline.
Click Use the classic editor.
For the source, select Azure Repos Git.
Make sure that the default branch is set to arm, and click Continue.
At the top of the page, click Empty job.
Select Agent job 1, and ensure the Agent pool value is set to Default.
Click the + next to Agent job 1.
Search for Node.js, and add the Node.js tool installer to the pipeline.
Search for and add npm to the pipeline.
Select the npm install tile, and change the Display name to install jsonlint.
For Command, select custom.
In the Command and arguments section, paste the following command:
install jsonlint -g
Click the plus sign next to Agent job 1 to add a new task.
Search for cmd, and add the Command line task.
Select the new Command Line Script task, and change the Display name to run jsonlint.
In the Script field, paste the following command:
jsonlint template.json
Run the Build Pipeline and Fix the Error
Click the Save & queue dropdown, and select Save & queue from the list.
Click Save and run.
Click the Agent job 1 build to monitor the progress.
Once it runs, observe that an error occurs. Click run jsonlint and check for the error.
In the left-hand menu, click Repos.
Open the template.json file, and click Edit to correct the errors.
Scroll to line 132, and remove the duplicate comma.
Click Commit > Commit.
Re-run the Build Pipeline
In the left-hand menu, click Pipelines, and select the MyFirstProject pipeline from the list.
Click Run pipeline > Run.
Click the Agent job 1 build to monitor the progress.
Once the build is complete, click Pipelines from the left-hand menu.
Select the vertical three-dot menu next to the MyFirstProject pipeline. Then, click Edit.
Click the plus sign next to Agent job 1 to add a new task.
Search for Copy and add a Copy Files task to the pipeline.
Select the Copy files task, and in the Target folder field, paste the following variable code:
$(Build.ArtifactStagingDirectory)
Click the plus sign next to Agent job 1 to add a new task.
Search for Publish build, and add a Publish build artifacts task to the pipeline.
Click the Save & queue dropdown, and select Save & queue from the list.
Click Save and run.
Click the Agent job 1 build to monitor the progress.
Once the job completes, click the 1 artifact link in the job's output to view the published artifact.
Create the Release Pipeline
In the left-hand menu, under Pipelines select Releases, and click New pipeline.
Click Empty job.
Click Add an artifact.
For the Source type, select Build.
For the Source (build pipeline) field, select MyFirstProject-CI.
Click Add.
Under Stages, click 1 job 0 task.
Select the Agent job tile and ensure the Agent pool field is set to Default.
Click the plus sign next to Agent job to add a new task.
Search for arm and add an ARM template deployment task to the pipeline.
Click the ARM Template deployment task, and notice that it requires a service connection. Click Manage next to Azure Resource Manager connection. A new tab will open.
Create Service Principal
Click Create service connection.
Select Azure Resource Manager.
Click Next.
Select Service principal (manual).
Click Next.
Go to your main Azure portal tab (the first tab you had open). Click the Cloud Shell icon (>_) in the upper right.
Select PowerShell.
Click Show advanced settings.
For Storage account, select Create new and give it a globally unique name (e.g., "storage" with today's date).
For File share, select Create new and give it a name of fileshare.
Click Create storage.
In PowerShell, run the following command:
Get-AzSubscription
Copy the value under Id.
Back in the Service Connection tab, paste this value for the Subscription Id.
Back in the PowerShell tab, copy the value for Name.
Back in the Service Connection tab, paste this value for the Subscription Name.
From your lab credentials, copy the Application Client ID, and paste it in the Service Principal Id field.
From your lab credentials, copy the value for the Secret, and paste it in the Service principal key field.
Back in the PowerShell tab, copy the value for the Tenant ID.
Back in the Service Connection tab, paste this value for the Tenant ID.
Click Verify.
Scroll down to Service connection name field, and enter SP.
Click Verify and save.
Run the Release Pipeline
Back in the ARM Template deployment task of the release pipeline, refresh the Azure Resource Manager connection field, and select SP.
For the Subscription and Resource group fields, select the lab-provided options.
Make sure that the Location is set to the same location as the other lab-provided resources. This can be verified in the All resources page of the main Azure portal.
For the Template, click the three-dot menu to the right of the field, and navigate through the file structure to select template.json from the /drop folder. Then click OK.
At the top of the page, click Save > OK.
Click Create release.
Click Create.
Click Release-1, and open the Logs under Stage 1 to monitor the progress. It will take a few minutes to run.
Go back to your main Azure portal tab.
Search for and navigate to Virtual machines.
Observe the new linuxVM-arm VM that was just deployed with the release pipeline.
Conclusion
Congratulations — you've completed this hands-on lab!
