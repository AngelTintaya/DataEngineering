# DataEngineering
Data Engineering Project

## Steps
1. Create Repo
2. Clone Repo in VSCode
3. Create Resource Group: RGDataEngineering (rg-de-mdsai)
4. Create Storage Account
- Select LRS (Local Redundant Storage)
- Enable Hierarquical Namespace
5. Go to Storage Account
- Create Container: datalake
- Create Files: raw, bronze, silver, gold
6. Create Azure Databricks
- Workspace Name: ws-prod-de
- Manage Resource Group Name: RGDataEngineeringManaged
- Click in Create

7. Become Account Admin: Launch Databricks and make Account Console Available
- Click in Workspace
- Click in Manage Account
(If can not find that, go to webpage: https://accounts.azuredatabricks.net/login)
- Log in with the User Principal Name: Microsoft Entra Id -> Manage -> Users -> Our User -> User Principal Name (Reset password if necessary)
- Click in User Management
- Select our real user and assign us as Account Admin
(By default the Account Admin is only for the User Principal Name and not us)
- Now, we are going to be able to see the Manage Account Option when selecting the Workspace (If we click it, we are going to be able to see the "Account Console")

8. Create Storage Account (And metastore)
- Go to Resource Group
- Go to Storage Account
- Go to Data Storage
- Go to Containers
- Create Container: metastoreroot (Which will be use by the metastore)
(This will be the root container dedicated to metastore)
(All the data will be listed there for the manage tables)
- Create another Container: datalake
9. Create Databricks Access Conector
- Go to our Resource Group: RGDataEngineering
- Search for "Access Conector"
- Select Access Connector for Azure Databricks
- Click in Create
- Name: accessModernDE
- Click in: Review + Create
- Click in Create
10. Set Up Access Connector Permissions
(Note: In order to be able, user should have access to UserAccessADministration. Request it to Global Administrator)
- Go to our Storage Account: storagemodernde
- Click in Access Control (IAM)
- Click in Add
- Click in Add Role Assignment
- Search for role: Storage Blob Data Contributor
- Click on Next
- Select Manage Identity
- CLick in Select Members
- As Managed Identity, select: Access Connector for Azure Databricks
- As Select, choose: Our Access Connector: accessModernDE
- Click in Select
- CLick in Review + Assign
- Click in Review + Assign (Again)
(Now It will assign that role to the access connector, so it can use the Storage Account)
11. Enable Unity Catalog (Includes Provide Access Connector to Databricks)
- Go to Account Console
- Go to Catalog
- Delete Current default Metastore (Click on it, then delete it)
- Click in Create Metastore
- Name: metastoreModernDE
- Region: eastus
- ADLS Gen 2 path (Location for storing managed tables): metastoreroot@...
- Access Connector ID: (Collect from Access Connector -> Resource ID)
- Click on Create (To Create the Metastore)
- (To Connect our Unity Metastore to the Databricks Workspace to enable Unity Catalog. So we are going to be able to create more catalogs)
- Select our Workspace: ws-prod-de
- Click Assign
- Click in Enable (Update any automation for principal/group management, such as SCIM, Okta and Microsoft Entra connectors, and Terraform to reference account endpoints instead of workspace endpoints
)
- We can close the Account Console now

12. Creating Cluster:
    A.Multi Node
    - Click in Compute
    - Click in Create Compute
    - Policy: Unrestricted
    - Select Multi Node
    - Access Mode: Standard (Formerly Shared)
    - Runtime: 15.4 LTS (Scala 2.12, Spark 3.5.0)
    - Uses Photon Acceleration
    - Node Type:
        Standard_DS3_v2 14 GB Memory, 4 Cores (General Purpose) or
        Standard_D3_v2 14 GB Memory, 4 Cores (General Purpose HDD)
        (If can not create, choose: Standard_D4s_v3 16 GB Memory, 4 Cores)
    - Min - Max Workers: 1-1
    - Spot Instances: Should be checked
    - Driver Type: Same as worker
    - Enable autoscaling
    - Terminate after: 10 min of inactivity
    - Click in Create Compute
    B. Single Node
    - Click in Compute
    - Click in Create Compute
    - Policy: Unrestricted
    - Select Single Node
    - Access Mode: Dedicated (Formerly Single User)
    - Runtime: 15.4 LTS (Scala 2.12, Spark 3.5.0)
    - Uses Photon Acceleration
    - Node Type:
        Standard_DS3_v2 14 GB Memory, 4 Cores (General Purpose) or
        Standard_D3_v2 14 GB Memory, 4 Cores (General Purpose HDD)
        (If can not create, choose: Standard_D4s_v3 16 GB Memory, 4 Cores)
        Standard_D4s_v3 16 GB Memory, 4 Cores
    - Min - Max Workers: 1-1
    - Spot Instances: Should be checked
    - Driver Type: Same as worker
    - Enable autoscaling
    - Terminate after: 10 min of inactivity
    - Click in Create Compute
    C.Multi Node
    - Click in Compute
    - Click in Create Compute
    - Policy: Unrestricted
    - Select Multi Node
    - Access Mode: Standard (Formerly Shared)
    - Runtime: 15.4 LTS (Scala 2.12, Spark 3.5.0)
    - Uses Photon Acceleration: Unchecked
    - Node Type:
        Standard_DS3_v2 14 GB Memory, 4 Cores (General Purpose) or
        Standard_D3_v2 14 GB Memory, 4 Cores (General Purpose HDD)
        (If can not create, choose: Standard_D4s_v3 16 GB Memory, 4 Cores)
    - Uncheck Enable Autoscaling (Se we should have 1.5 DBU/h)
    - Workers: 1
    - Spot Instances: Should leave unchecked
    - Driver Type: Same as worker
    - Terminate after: 15 min of inactivity
    - Click in Create Compute

    D.Single Node (Best Option)
    - Click in Compute
    - Click in Create Compute
    - Policy: Unrestricted
    - Select Single Node
    - Access Mode: Dedicated (Single User): Add the GROUP
    - Runtime: 15.4 LTS (Scala 2.12, Spark 3.5.0)
    - Uses Photon Acceleration: Checked
    - Node Type:
        Standard_DS3_v2 14 GB Memory, 4 Cores (General Purpose) or
        Standard_D3_v2 14 GB Memory, 4 Cores (General Purpose HDD)
        (If can not create, choose: Standard_D4s_v3 16 GB Memory, 4 Cores)
    - Terminate after: 15 min of inactivity
    - Click in Create Compute

13. Give metastore admin access to our user (We can not create catalogs, because we are not metastore admins)
- Go to Account Console (Click in our workspace:, click in Manage Account)
- Click in Catalog
- Click in our Metastore: metastoremodernde
- Go to Metastore Admin
- Click in edit
- Write the name of the new metastore admin (Out email)
- Click Save
- Now in catalog we are going to be able to create catalog (Because we are METASTORE ADMINs)

14. Create Notebook
- Go to workspace
- Click in workspace
- Click in Create
- Click in Folder
- Name them: ModernDE
- Now, Click Create, then notebook
- Name it as: tutorial_1

15. Create External Location CREDEMTIALS, it is necessary to have unity catalog by this momment
(In order to have access to the data)
NOTE: A catalog can (and should) be attached to an external location,
otherwise it will store the tables in the metastore location, which is metastoreroot,
so when not defined it will me a managed table

(We connect our workspace to Databricks through External Location
and for that, we need an access connector
So, we need to add the access connector to the external location.
Once it is attached, it is called: Storage Credential
<We are creating an ID in order to give it to the external location>)
- Go to catalog
- Click External Data
- Click in credentials (There should be one by default <For the manage table>)
- Click in create Credential
- Leave storage credential selected
- Leave Credential Type as Azure Managed Identity (default one)
- Give a Credential name: atmcreds
- Give the access connector ID: (Go to access connector and copy the resource ID)
- Click in create

16. Create External Location (From our container level)
- Go to catalog
- Click External Data
- Click Create External Location
- Name it as: myextloc
- Copy the url considering the container: datalake
- In storage credential: Select the one that was created : atmcreds
- Click in create
- Now we can read or create data into that container

# Adding external users into databricks workspace
1. Adding External Users in Azure:
- Go to Azure Portal
- Search for "Users"
- For massive invitations: Click in Bulk Operations -> Download and Upload csv with all users -> Invite OR
- Click in "New user" (For individual invitation)
- Click in "Invite External user"
- Write Email Address, Display Name
- Click in Review+Create
- Click in Invite

2. Accept Invitation (User)
- En email will be sent to the user
- User should accept invitation from Azure
- It will launch Azure Portal to log in
- Log in with invited email

3. Give permission to user to the suscription
- Go to suscription
- Go to Access Control (IAM)
- Click in Add
- Click in "Role Assignment"
- In Role, click in "Reader"

- In Role, click in "Privileged Administrator Roles" tab
- Click in Role Based Access Control Administrator
- (When selecting member and clicking continue, in conditions choose: Allow User to assign all roles)

- Click Next
- Click in Select Members
- Choose Member
- Click in Select
- Click in review and assign

4. User: Check if was added to Azure Portal
- Log into Azure Portal
- Go to accounts (Your photo icon)
- Click on Switch directory
- Switch with the domain that invited you (Only if previously, already had directory)
- User will be able to see the Azure Account that was invited
- User can now launch databricks

5. Add External User to Databricks
- Go to Azure Databricks account
- Go to the Account Console (Click in the workspace -> Manage Account)
- Go to User Management
- In "Users" tab, click "Add user"
- Write user email (the one was send to the user) and Fullname
- Now, the user is added

6. Create a Group
- In Account Console
- In User Management
- In "Groups" tab, click "Add Group"
- Create a Group for that user
- Define a Group Name
- Click "Add Group"
- Click in "Add Member"
- Add the user (We should be able to write part of the name and see the complete user)
- Click in Add

7. Add permission to the Workspace
- In Account Console
- Go to Workspace tab
- Click in the workspace we want the user to add
- CLick in Permissions Tab 
- Click in Add Permissions
- Select the group (or user) to add to the workspace
- Give "User" permission (Other option is admin)
- Now all people that belong to the group can access to the databricks workspace
- User can check he can access to databricks workspace

8. Review users folder
- As admin
- Go to workspace
- Go to Users
- We can see the user we added now, meanwhile the added user will see only its own user.

9. Permissions to use a cluster
- Go to databricks
- Go to compute
- Create or select a compute
- Click in More (or 3 dots, in the right part)
- Click in Permissions
- Add Group, permission to: Can Restart (Also Can Manage or Can Attach To)
- Click in Add
- Click in Save

# Creating Git Folder into Databricks Account
1. Get clone URL from repo in Github
2. Go to Databricks
3. Go to workspace
4. Click in workspace
5. Click in users
6. Click in Create (in the right side)
7. Click in git folder (Name will be by default the name of the repo)
8. Click in Create (Now we get the repo inside our current user)
(Note: We have created the git folder in the databricks account
but we still do not connected our github account with databricks repositories)

# Connect Github Account wiith Databricks Repo
(We need to do this, otherwise we can modify repo in databricks but we will not be able to pull changes)
1. Click in our User Profile (Right side)
2. Click in Settings
3. Below user, click in Linked Accounts (We will see there: Git Integration)
4. In Git Provider: Choose GitHub
5. Select "Link Git Account"
6. Click Link (It will link by default to the Github Account you are logged in)
7. Click in configure in Github
8. It will create a Databricks account that will connect to your github
9. We must connect to the repo you want it link (By default it will point to all repos)
- Select "Only select repositories" (instead of All repositories)
- Click on Install
- It will take you to a webpage in your github account in case we want to make updates
- Everything is set up now.

# Use Databricks Git
1. We can go to our workspace
2. In Home we can see our Repo, we can click in the branch ("develop")
3. We are going to see the changes there now.
4. Click in "Create Branch"
5. Name it: feature/add-requirements-file (Based on Develop)
6. We can see we are working in our own branch now
7. Create requirements.txt in the repo
8. In the repo, click in the branch
9. It will capture the changes
10. Define a commit message: chore: initialize Python dependencies with requirements.txt
11. Click in commit Commit & Push
12. It now made un update and loaded the branch with the changes in github
13. A message will recomend you to do a "Pull request", click on it (Or Create a PR in Github)
14. Make sure you will do a PR from your current branch to develop.
15. Review only your changes were made
16. Click Create PR.
17. Click "Create Pull Request"
18. Wait for Owner to accept PR
19. Click on Delete Branch
20. Now the code should be updated.

# Git: Develop has changes and in local I also have chages
- git fetch origin: To get the latest commit
- Stage current changes
- git rebase origin/develop: re-apply our local commit on top of remote develop

# If can not push to develop because a rule that force PR
- create a new branch (It will take your changes)
- Push the new branch
- Do a PR in Github
- Go to develop
- Do a git pull origin develop
- Now develop is up to date.
- Make sure to delete unnecessary branchs in local

# Delete unncecessary branches in local
- Checkout to other branch (develop)
- git branch -d branch_name: Will delete the branch
- git remote prune origin: Will remove deleted branch from logs

# Sync Github in AzureDatabricks (Alumns)

# Upgrading Databricks to Premium

Resources:
- Resource Group:           RGDataEngineering           |   rg-de-mdsai
- Storage Account:          storagemodernde             |   stdemdsai
- Container:                datalake                    |   datalake
- Cont. datalake Files I:    raw, bronze, silver, gold
- Cont. datalake Files II:   external_catalog_loc, external_table
- Container:                metastoreroot               |   metastoreroot
- Databricks Workspace:     ws-prod-de                  |   ws-dev-de-mdsai
- Dbk Manage RG:            RGDataEngineeringManaged    |   rg-de-mdsai-managed
- Dbk Access Connector:     accessModernDE              |   accessModernDE
- Dbk Metastore:            metastoreModernDE           |   metastoreModernDE
- Dbk Storage Credential:   atmcreds                    |   atmcreds
- Dbk External Location:    myextloc                    |   myextloc