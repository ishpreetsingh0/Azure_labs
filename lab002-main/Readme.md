Deploy a Python App to an AKS Cluster Using Azure Pipelines

https://learn.acloud.guru/handson/73ed05ea-f8b5-48bb-8767-bddeea0ff4bc

Introduction
You are responsible for deploying a Python app to AKS. You have the code and a pipeline template, and you must create a CI/CD pipeline in Azure DevOps.
Solution
Log in to the Azure portal using the credentials provided for the lab.
Create an Azure DevOps Organization
1. In the main search bar, search for and select Azure DevOps organizations.
2. Scroll down and click My Azure DevOps Organizations.
3. Select your country/region from the dropdown list.
4. Select Create new organization > Continue > Continue (complete the required CAPTCHA).
5. Set the following values to create a project:
    * Project name: MyFirstProject
    * Visibility: Private
6. Click + Create project.
Import Code and Set Up the Environment
Create Your Environment
1. In the sidebar menu, select Repos.
2. In the Import a repository section, click Import.
3. In the Clone URL field, enter the following repository: https://github.com/ACloudGuru-Resources/content-az400-lab-resources.git 
4. Click Import. After the import completes successfully, the Files page should open automatically.
5. At the top of the page, use the dropdown to switch the branch from DSC to aks. You should now see the files you need for the lab.
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
8. Log in to the lab-VM Linux virtual machine using the credentials provided in the lab environment: ssh cloud_user@<PUBLIC_IP> 
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
3. When prompted for your server URL, go back to Azure DevOps and grab the URL from your browser. It will look something like https://dev.azure.com/clouduser..., followed by some characters (i.e., copy everything up to and not including /MyFirstProject). Paste this in the terminal.
4. Press Enter to accept the default authentication type of PAT.
5. For personal access token, copy and paste the personal access token you created and saved earlier in this lab.
6. Press Enter to accept the default agent pool.
7. Press Enter to accept the default name.
8. Press Enter to accept the default work folder.
9. Run the agent interactively so that it is always listening for any jobs: ./run.sh 
Create Pipeline Environment
1. In the sidebar menu, select Pipelines, then select Environments.
2. Click Create environment.
3. Fill in the environment details:
    * Name: Enter dev.
    * Resource: Select Kubernetes.
4. Click Next.
5. Ensure the resource details auto-populate, then click Validate and create.
Create a Service Connection and an ACR
1. In the bottom-left corner, click Project Settings. This opens the settings in a new tab.
2. In the sidebar menu, select Service connections.
3. In the top right, click New service connection.
4. In the pane on the right, select Docker Registry, then click Next. You'll need to gather some information from an Azure Container Registry (ACR) before you finish creating your service connection.
5. Navigate back to your Azure DevOps tab.
6. Navigate to All resources using the hamburger menu in the top-left corner.
7. Select the link for the Azure container registry (ACR) that's been pre-deployed with the lab.
8. In the ACR's sidebar menu, select Access keys (under Settings). You'll use these details for your service connection.
9. Add the New Docker Registry service connection details:
    * Fill in the Docker Registry:
        * From the Access keys page, copy the Login server.
        * Navigate to the service connections tab and paste the login server into the Docker Registry field using the format https://<LOGIN_SERVER>.
    * Fill in the Docker ID:
        * Navigate to the ACR tab and copy the Username.
        * Navigate to the service connections tab and paste the username into the Docker ID field.
    * Fill in the Docker Password:
        * Navigate to the ACR tab and copy either password.
        * Navigate to the service connections tab and paste the password into the Docker Password field.
    * In the Service connection name field, enter ACR.
    * Below Security, check the Grant access permission to all pipelines checkbox.
10. Click Save.
Create the CI/CD Pipeline
Update the Deployment Image
1. From the service connections tab, use the sidebar menu to select Repos.
2. Select the manifests folder, and open the vote.yml file.
3. In the top right, click Edit.
4. Update the deployment image:
    * Navigate to the ACR tab and copy the Login server again.
    * Navigate to the vote.yaml tab and replace the container image on line 59 with the Azure ACR DNS registry name, following the format <LOGIN_SERVER>/azure-vote-front:v1.
5. Note: The ACR DNS name ends with azurecr.io. 
6. In the top right, click Commit.
7. In the Commit pane, ensure the Branch name is set to aks, then click Commit.
Create the Pipeline
1. In the sidebar menu, select Pipelines.
2. Click Create Pipeline.
3. For the code location, select Azure Repos Git.
4. For the repository, select the MyFirstProject repo.
5. Select Existing Azure Pipelines YAML file.
6. Fill in the YAML file details:
    * Branch: Select aks.
    * Path: Select /azure-pipelines.yml.
7. Click Continue.
8. Update the azure-pipelines.yml code:
    * Navigate to the ACR tab and copy the Login server again.
    * Navigate to the pipeline tab and replace the value for containerRegistry with your copied Azure ACR DNS registry name.
9. In the top-right corner, click Save and run.
10. In the pane on the right, leave the default settings and click Save and run.
11. After the pipeline is triggered, select Build stage to monitor the build progress. Note the banner in the terminal indicating that the pipeline needs additional permissions to continue the build.
12. Click View to the right of the banner.
13. Click Permit to the right of the requested permissions and confirm the selection.
14. After the build has completed, select Deploy to dev. Note the banner in the terminal indicating that the pipeline needs additional permissions to continue the deployment. If the banner does not display. Click the back arrow above the deployment to go back to the job summary. Then, click View in the yellow notification box.
15. Like before, allow all permissions. The deployment will take a few minutes to complete.
Access the AKS Cluster
1. Navigate to the ACR tab.
2. At the top, click All resources.
3. Select the link for the Kubernetes service cluster. (You can ignore any errors that appear as they are related to restrictions on the lab environment.)
4. On the left menu, under Kubernetes resources, select Services and ingresses.
5. All services in the cluster are displayed. Observe the azure-vote-front service has an External IP. Click the link for the IP.
6. The app opens in a new tab where you can click to vote on cats or dogs.
—-
yml file

trigger:
- aks

variables:
  dockerRegistryServiceConnection: 'ACR'
  imageRepository: 'azure-vote-front'
  containerRegistry: acr33c6dhacytdq6.azurecr.io
  dockerfilePath: '**/Dockerfile'
  tag: '$(Build.BuildId)'
  imagePullSecret: 'csacr5d93-auth'  

stages:

- stage: Build
  displayName: Build stage
  jobs:  
  - job: Build
    displayName: Build
    pool:
      name: Default
    steps:
    - task: Docker@2
      displayName: Build and push an image to container registry
      inputs:
        command: buildAndPush
        repository: $(imageRepository)
        dockerfile: $(dockerfilePath)
        containerRegistry: $(dockerRegistryServiceConnection)
        tags: |
          $(tag)
          
    - upload: manifests
      artifact: manifests

- stage: Development
  displayName: Deploy to dev
  dependsOn: Build

  jobs:
  - deployment: Deploy
    displayName: Deploy to dev
    pool:
      name: Default
    environment: 'dev.default'
    strategy:
      runOnce:
        deploy:
          steps:
          - task: KubernetesManifest@0
            displayName: Create imagePullSecret
            inputs:
              action: createSecret
              secretName: $(imagePullSecret)
              dockerRegistryEndpoint: $(dockerRegistryServiceConnection)
              
          - task: KubernetesManifest@0
            displayName: Deploy to Kubernetes cluster
            inputs:
              action: deploy
              manifests: |
                $(Pipeline.Workspace)/manifests/vote.yml
              imagePullSecrets: |
                $(imagePullSecret)
              containers: |
                $(containerRegistry)/$(imageRepository):$(tag)


—-
Docker file
FROM tiangolo/uwsgi-nginx-flask:python3.6
RUN pip install redis
ADD /azure-vote /app


—
Vote.yml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: acr33c6dhacytdq6.azurecr.io/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front

