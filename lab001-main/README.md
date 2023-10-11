# lab001
Lab for Create a Multi-Stage Build in Azure Pipelines to Deploy a .NET App
url to follow: https://learn.acloud.guru/handson/d6131f74-521d-434c-91c9-ba40665f59b2
Create a Multi-Stage Build in Azure Pipelines to Deploy a .NET App

Description
Your challenge is to deploy a .NET app to Azure using Azure DevOps. More specifically, you must create a multi-stage build, using YAML. The code has been provided, and you must create the build, and deploy operations.
Objectives
Successfully complete this lab by achieving the following learning objectives:


Create an Azure DevOps Organization
1. Log in to the Azure portal using the provided credentials.
2. Navigate to Azure DevOps.
3. Create a new organization and project named MyFirstProject.


Import Code and Set Up Environment
1. Import the code from this GitHub repository: https://github.com/ACloudGuru-Resources/content-az400-lab-resources.git.
2. Create a service connection for Azure Resource Manager using the service principal provided.


Create the CI/CD Pipeline
1. This lab requires the use of a self-hosted agent. Instructions can be found here: Self-Hosted Agent Instructions.
2. Create a YAML template with a build and deploy stage.
3. Include a task to use .NET Core version 3.1.x.
4. Make sure BOTH stages are using the self-hosted agent. HINT: Use the assistant in Azure DevOps to help with YAML build syntax. 
Optionally, you can use the provided YAML template

cloud_user_p_4302a3dd@realhandsonlabs.com

Subcription name : <name>
Id : 
Tenant id: 

yml pipeline
trigger:
- stage

variables:
  buildConfiguration: 'Release'

stages:

- stage: Build
  jobs:
  - job: Build
    pool:
      name: default
    steps:
    - task: UseDotNet@2
      inputs:
        packageType: 'sdk'
        version: '3.1.x'
    
    - task: DotNetCoreCLI@2
      inputs:
        command: 'restore'
        projects: '**/*.csproj'
        feedsToUse: 'select'

    - task: DotNetCoreCLI@2
      inputs:
        command: 'build'
        projects: '**/*.csproj'

    - task: DotNetCoreCLI@2
      inputs:
        command: 'publish'
        publishWebProjects: true
        arguments: '--configuration $(BuildConfiguration) --output $(Build.ArtifactStagingDirectory)'

    - task: PublishBuildArtifacts@1
      inputs:
        PathtoPublish: '$(Build.ArtifactStagingDirectory)'
        ArtifactName: 'drop'
        publishLocation: 'Container'

- stage: Deploy
  jobs:
  - job: Deploy
    pool:
      name: default
    steps:
    - checkout: none

    - download: current
      artifact: drop
      
    - task: AzureWebApp@1
      inputs:
        azureSubscription: 'SP'
        appType: 'webAppLinux'
        appName: 'webappiwbu4e5a7cv2e-webapp'
        package: '$(Pipeline.Workspace)/**/*.zip'

— Create a Multi-Stage Build in Azure Pipelines to Deploy a .NET App
Introduction
Your challenge is to deploy a .NET app to Azure using Azure DevOps. More specifically, you must create a multi-stage build, using YAML. The code has been provided, and you must create the build, and deploy operations.
Solution
Log in to the Azure portal using the credentials provided on the lab instructions page.
Create an Azure DevOps Organization
1. In the main search bar, search for and select Azure DevOps organizations.
2. Scroll down and click My Azure DevOps Organizations.
3. Select your country/region from the dropdown list.
4. Click Continue.
5. Select Create new organization > Continue > Continue (complete the required CAPTCHA).
6. Set the following values to create a project:
    * Project name: MyFirstProject
    * Visibility: Private
7. Click + Create project.
Import Code and Set Up Environment
Import Code
1. In the left-hand menu, click Repos.
2. In the Import a repository section, click Import.
3. In the Clone URL field, paste the following repo URL: https://github.com/ACloudGuru-Resources/content-az400-lab-resources.git.
4. Click Import.
5. Above the repo file list, click the branch selector (currently says DSC), and select the stage branch. Review the files that will be used in the lab.
Create Personal Access Token
1. In the upper right-hand corner, click the User settings icon > Personal access tokens.
2. Click + New Token and set the following values:
    * Name: pat1
    * Scopes: Full access
3. Click Create.
4. Copy the pat1 token and save it in a safe location.
5. Click Close.
Download Self-Hosted Agent
1. At the top of the page, click the Azure DevOps icon, then select the project.
2. On the bottom left, click Project settings.
3. Scroll down the left-hand pane and under Pipelines, select Agent pools.
4. Select the Default pool.
5. Click New agent.
6. Select the Linux tab.
7. Click the Copy icon, next to Download to copy the agent URL to your clipboard. Paste it to a text file to use in the following steps. Keep this tab and popup open.
8. Log in to the labVM Linux virtual machine using the credentials provided in the lab environment: ssh cloud_user@<PUBLIC_IP> 
9. Change directory to the root: cd ~ 
10. Make and navigate to a new directory called Downloads: mkdir Downloads && cd Downloads 
11. Run wget against the agent URL you just copied: wget <AGENT_URL> 
12. Navigate back to the root (cd ~).
13. Create and navigate to another directory called myagent: mkdir myagent && cd myagent 
14. Unzip the package you just downloaded. You can copy this same command from the popup in Azure DevOps to get the full package and version number: tar zxvf <PACKAGE> 
15. Review the contents of the directory (ls). Observe all of the files and folders for the agent.
Configure the Agent
1. Run the configuration script: ./config.sh 
2. Type y to accept the license agreement.
3. When prompted for your server URL, go back to Azure DevOps and grab the URL from your browser. It will look something like https://dev.azure.com/clouduser..., followed by some numbers (i.e., copy everything up to and not including /MyFirstProject). Paste this in the terminal.
4. Press Enter to accept the default authentication type of PAT.
5. For personal access token, copy and paste the personal access token you created and saved earlier in this lab.
6. Press Enter to accept the default agent pool.
7. Press Enter to accept the default name.
8. Press Enter to accept the default work folder.
9. Run the agent interactively so that it is always listening for any jobs: ./run.sh 
Create Service Connection
1. Go back to Azure DevOps.
2. At the bottom of the Project Settings navigation pane, under Pipelines, click Service connections.
3. Click Create service connection.
4. Select Azure Resource Manager, and click Next.
5. Select Service principal (manual), and click Next.
6. Return to your main Azure portal browser tab, and open Cloud Shell by clicking on the icon immediately to the right of the search bar (looks like a caret (>) inside of a square).
7. Select PowerShell.
8. Click Show advanced settings.
9. For Storage account, click Create new. Enter a unique name.
10. Under File share, select Create new, and type fileshare.
11. Click Create storage.
12. Once you are connected to the Cloud Shell, run the following command: Get-AzSubscription 
13. Copy the subscription ID under Id.
14. Paste the subscription ID into the field under Subscription Id (in the New Azure service connection pane).
15. Back in PowerShell, copy the subscription name under Name.
16. Return to Azure DevOps, and paste the subscription name into the field under Subscription Name.
17. Under Service Principal Id and Service Principal key, paste in the principal ID and principal key provided in your hands-on lab credentials (listed as Application Client ID and Secret, respectively). These credentials are in a box labeled Service Principal. You may have to scroll or click the arrow to the right of your provided Azure credentials to see this box.
18. Return to PowerShell, and copy the tenant ID under TenantID.
19. Return to Azure DevOps, and paste the tenant ID into the field under Tenant ID.
20. Click Verify to check the settings.
21. In the Service connection name field, name the service connection SP for service principal.
22. Click Grant access permission to all pipelines.
23. Click Verify and save.
Create the CI/CD Pipeline
1. In the left-hand menu, click Pipelines.
2. Click Create Pipeline.
3. Click Azure Repos Git.
4. Select MyFirstProject from the list.
5. Click Existing Azure Pipelines YAML file.
6. For Branch, select stage.
7. For Path, select /pipeline.yml, and click Continue.
8. Scroll to the bottom of the file, and delete the < *** Add Task to deploy Azure Web App here *** > comment. Ensure your cursor remains indented 4 spaces.
9. Above the code pane, click Show assistant.
10. In the search box, search for and select Azure Web App.
11. For the Azure Subscription, select SP from the dropdown.
12. For App type, select Web App on Linux
13. For the App name, select the only option in the dropdown. Note: If the name does not display, go back to the Azure portal. Then, navigate to App Services. Copy and paste the name of the web app and paste it to the App name field. 
14. Update the Package or folder field to use the following package: '$(Pipeline.Workspace)/**/*.zip' 
15. Click Add.
16. Click Save and run > Save and run.
17. You will see a message noting required permissions. Click the Build tile, under Stages.
18. In the permissions message, click View. Then, click Permit > Permit.
19. Once the Build stage is complete, select the Deploy stage to monitor the deployment process. Both steps may take a few minutes to complete.
20. Once deployed successfully, go back to the Azure portal.
21. Search for and navigate to App Services.
22. Click the web app from the list.
23. Click the app URL (in the Default domain field), and verify the app is accessible.
Conclusion
Congratulations — you've completed this hands-on lab!
