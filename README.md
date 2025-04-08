# DataEngineering
Data Engineering Project

## Steps
1. Create Repo
2. Clone Repo in VSCode
3. Create Resource Grou: RGDataEngineering
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
7. Launch Databricks and make Account Console Available
- Click in Workspace
- Click in Manage Account
(If can not find that, go to webpage: https://accounts.azuredatabricks.net/login)
- Log in with the User Principal Name: Microsoft Entra Id -> Manage -> Users -> Our User -> User Principal Name (Reset password if necessary)
- Click in User Management
- Select our real user and assign us as Account Admin
(By default the Account Admin is only for the User Principal Name and not us)
- Now, we are going to be able to see the Manage Account Option when selecting the Workspace (If we click it, we are going to be able to see the "Account Console")

8. Create Storage Account
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
12. Creating Cluster
- Click in Compute
- Click in Create Compute
- Policy: Unrestricted
- Select Single Node
- Runtime: 15.4 LTS (Scala 2.12, Spark 3.5.0)
- Node Type: Standard_DS3_v2 14 GB Memory, 4 Cores (General Purpose)
(If can not create, choose: Standard_D4s_v3 16 GB Memory, 4 Cores)

- Terminate after: 20 min of inactivity
- Click in Create Compute

 

