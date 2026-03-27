## **Lab 3- Migrating Azure Synapse Spark and Lake Database to Microsoft Fabric**

**Introduction**

In this lab, you will learn how to migrate data and workloads from Azure
Synapse Analytics to Microsoft Fabric. The lab covers creating and
configuring a Synapse workspace, uploading and accessing data from Azure
Data Lake Storage Gen2, and setting up a Microsoft Fabric workspace with
a Lakehouse.

You will also explore how to use shortcuts to connect external storage,
transform data using notebooks, and rebuild data pipelines in Microsoft
Fabric. This hands-on exercise provides practical experience in
modernizing data platforms and implementing a unified analytics
solution.

**Objective**

By the end of this lab, you will be able to:

- Create and configure an Azure Synapse workspace and dedicated SQL pool

- Upload and manage data in Azure Data Lake Storage Gen2

- Set up a Microsoft Fabric workspace and Lakehouse

- Create shortcuts to access external data in OneLake

- Transform and load data using notebooks

- Rebuild and execute data pipelines in Microsoft Fabric

- Validate migrated data and resources

## Task 0: Understand the VM and the credentials

In this task, we will identify and understand the credentials that we
will be using throughout the lab.

1.  **Instructions** tab hold the lab guide with the instructions to be
    followed throughout the lab.

2.  **Resources** tab has got the credentials that will be needed for
    executing the lab.

    - **URL** – +++https://portal.azure.com+++

    - **Subscription** – @lab.CloudSubscription.Name

    - **Username** – +++@lab.CloudPortalCredential(User1).Username+++

    - **Password** – +++@lab.CloudPortalCredential(User1).AccessToken+++

    - **Resource Group** – @Lab.CloudResourceGroup(ResourceGroup1).Name

	>[!Note] Make sure you create all your resources under
this Resource group

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image1.png)

3.  **Help** tab holds the Support information. The **ID** value here is
    the **Lab instance ID** which will be used during the lab execution.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image2.png)

## Task 1: Create a Synapse workspace in the Azure portal

1.  Open a browser go to +++https://portal.azure.com+++ and sign in with
    your cloud slice account below.

	Username: +++@lab.CloudPortalCredential(User1).Username+++

	Password: +++@lab.CloudPortalCredential(User1).Password+++

    ![A screenshot of a computer Description automatically generated](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image3.png)

    ![A screenshot of a login box Description automatically generated](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image4.png)

2.  Search for +++Synapse Analytics+++ and select **Azure Synapse
    Analytics.**

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image5.png)

3.  Click **Create**.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image6.png)

4.  Enter below details to create resource group and then click
    on **OK**.

    1.  **Subscription**: @lab.CloudSubscription.Name

    2.  **Resource Group**: @Lab.CloudResourceGroup(ResourceGroup1).Name

    3.  **Managed Resource group:**  Leave this blank.

    4.  **Workspace name**: +++fabric-synapse@Lab.LabInstance.ID+++

    5.  **Region**: @Lab.CloudResourceGroup(ResourceGroup1).Location

	- **Select Data Lake Storage Gen2 account:** Create new

  1.  **Account name**: +++synapsegen2@lab.LabInstance.ID+++

  2.  Click **OK**

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image7.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image8.png)

5.  Enter below details and then click on **Next:Security**.

	**File System Name**: Create New: +++synapsefile@Lab.Labinstance.ID+++ and click **OK**.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image9.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image10.png)

6.  Configure the **Security** settings by selecting **both Microsoft
    Entra ID and SQL authentication**, then provide a valid **SQL admin
    username**: +++sqladmin+++ and **password**: +++password321!+++ to enable secure
    access to the Azure Synapse workspace.

7.  Click **Review + create**

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image11.png)

8.  In the **Review + submit** tab, once the Validation is Passed, click
    on the **Create** button.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image12.png)

9.  This deployment may take a few minutes.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image13.png)

10. Click on **Go to resource group** button.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image14.png)

11. Click on your **Synapse workspace**.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image15.png)

## Task 2: Create a dedicated SQL pool

1.  In Open Synapse Studio box, click on **Open** to launch your Azure
    Synapse studio.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image16.png)

	This launches the Synapse Studio interface.

2.  In Synapse Studio, select **Manage** (left pane).

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image17.png)

3.  In Synapse Studio, on the left-side pane, select **Manage** \> **SQL
    pools** under **Analytics pools** and then click on **New.**

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image18.png)

4.  **Configure SQL Pool**

	- **SQL pool name:** +++sql dedicated pool+++

	- **Performance level:** Choose DW100c (or any required level for
	  training)

	- Click **Review + Create → Create**.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image19.png)

5.  In the **Review + submit** tab, once the Validation is Passed, click
    on the **Create** button.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image20.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image21.png)

6.  Go to **Manage → SQL pools** and confirm the Dedicated SQL Pool
    shows **Online**

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image22.png)

	>[!Knowledge] A dedicated SQL pool consumes billable resources as long
	as it's active. You can pause the pool later to reduce costs. Please
	Pause it if you not performing labs for the day or even for 30 minutes
	to save your credits.

	Make sure to resume your SQLPool before you start your labs.

7.  In the Overview section of the Synapse workspace home page, copy the
    **Dedicated SQL endpoint and Dedicated SQL pool**, and save them in
    a notepad.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image23.png)

Task 3: Upload Sample Data into the Primary Storage Account

Open Synapse Studio

1.  In Synapse Studio, navigate to the **Data Hub**. Select **Linked**.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image24.png)

2.  Under the category **Azure Data Lake Storage Gen2** you'll see an
    item with your workspace name like **fabric-synapse@Lab.LabInstance.ID (Primary
    -- synapsegen2@lab.LabInstance.ID**

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image25.png)

3.  Select **+ New folder**

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image26.png)

4.  Enter the folder name as +++FabricMigration+++ and click the
    **Create** button.
    
	![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image27.png)

6.  Select **FabricMigration** folder

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image28.png)

4.  Click **Upload**.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image29.png)

5.  Click on **Folder**.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image30.png)

12. Browse to **C:\Lab Files** on your VM, then
    select **all** file except DACPAC file and click on **Open** button.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image31.png)

6.  After selecting all the required files, click the **Upload** button
    to add them to the destination folder.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image32.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image33.png)

7.  Right‑click on the folder and select **Properties.**

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image34.png)

8.  In Properties page, copy **URL** path and save it in notepad to use
    them in further tasks

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image35.png)

9.  Right‑click on the **DimCustomer.csv** and select
    **Properties.**

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image36.png)

10. In Properties page, copy **ABFSS** path and save it in notepad to
    use them in further tasks

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image37.png)

    >[!Note] URL and AFSS path might be different for your account.

## Task 3: Create a Fabric workspace

In this task, you set up a new Microsoft Fabric workspace to serve as
the central hub for the Medallion Architecture implementation. This
workspace brings together all essential components, including
Lakehouses, Dataflows, Pipelines, Warehouses, Notebooks, and Power BI
assets.

1.  Open your browser, navigate to the address bar, and type or paste
    the following URL: +++https://app.fabric.microsoft.com/+++ then
    press the **Enter** button.

2.  On the **Fabric Home** page click on **+ New Workspaces** as shown
    in the image below.

    ![A screenshot of a computer AI-generated content may be
incorrect.](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image38.png)

	| Field                     | Value                                      |
	|--------------------------|--------------------------------------------|
	| Name                     | +++FabricMigrationLab@lab.LabInstance.Id+++ |
	| Advanced                 | Select Fabric                              |
	| Default storage format   | Small dataset storage format               |

3.  On the **Create a workspace** pane that appears to the right, enter
    the following details, and then click **Apply**.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image39.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image40.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image41.png)

## Task 4: Create Lakehouse

1.  In the **FabricMigrationLab@Lab.Labinstance.ID** workspace page, navigate and click
    on **+New item**  button

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image42.png)

2.  Click on the "**Lakehouse**" tile.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image43.png)

3.  In the **New lakehouse** dialog box, enter
    +++SynapseMigrationLakehouse+++ in the **Name** field and **unselect** *lakehouse schemas*, click on
    the **Create** button and open the new lakehouse.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image44.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image45.png)

## Task 5: Create Shortcut in the Files Section

1.  In the **Lakehouse**, select the **New Shortcut** tile

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image46.png)

2.  From the list of external sources, select **Azure Data Lake Storage
    Gen2** to create a new shortcut.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image47.png)

3.  Select **New connection**, enter the **ADLS Gen2 URI**, and then
    click **Next** to proceed with creating the shortcut.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image48.png)

4.  Select **synapsefile** and click **Next**

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image49.png)

5.  Click **Create**

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image50.png)

6.  Select the **synapsefile** folder to review the files.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image51.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image52.png)

7.  In the **Lakehouse** page, navigate and click on **Open
    notebook** drop in the command bar, then select **New notebook**.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image53.png)

8.  Replace all the code in the **cell** with the following code and
    click on **▷ Run cell** button and review the output.

	```
	df = spark.read.format("csv").load("Dimension_Customer File ABFSS URI ")

	Update the **Dimension_Customer** file with the **ABFSS URI** saved in
	**Task 2, Step 10**.

	**Example:**

	df =
	spark.read.format("csv").load("abfss://synapsefile@fabricsynapsegen21.dfs.core.windows.net/FabricMigration/Dimension_Customer.csv")
	```

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image54.png)

9.  Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output

	+++df.write.format("delta").mode("overwrite").save("Tables/Customer")+++

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image55.png)

10. To validate the created tables, click and select refresh on
    the **Tables** in the **Explorer** panel until all the tables appear
    in the list.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image56.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image57.png)

## Task 6: Rebuild Synapse Pipelines in Microsoft Fabric

1.  From the left menu, select workspace icon and then select workspace
    name.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image58.png)

2.  Select the **+ New item** option on the workspace page and
    select **Pipeline**

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image59.png)

3.  Provide a Pipeline Name as +++**Migrate_Pipeline+++** and then
    select **Create**.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image60.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image61.png)

4.  On newly created pipeline, select **Copy data** dropdown and
    choose **Add copy data activity** option.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image62.png)

5.  With the **copy data** being selected, navigate to **Source** tab.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image63.png)

6.  Select the **Connection** dropdown and select **Browse all** option.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image64.png)

7.  Select **+ New** from the left pane

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image65.png)

8.  From the data source options, select **Azure Data Lake Storage
    Gen2** to begin the connection setup.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image66.png)

9.  Select **New connection**, enter the **ADLS Gen2 URI**, and then
    click **Connect** to proceed with creating the shortcut.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image67.png)

10. In the **Source** tab of the Copy Data activity, click the
    **Browse** button to select the folder containing the source files.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image68.png)

11. Select the **Fact_Order.csv** file and click **OK**.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image69.png)

11. Now, navigate to **destination** tab and click **Browse all** tab

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image70.png)

12. On choose a destination window, select **OneLake catalog** from the
    left pane and select the **synapseMigrationLakehouse**

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image71.png)

13. In the **Source** tab, select **DelimitedText** as the file format.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image72.png)

14. Under the **Destination** tab, click **+ New**.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image73.png)

15. Enter the table name as +++dim_Date+++ and click **Create**.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image74.png)

8.  Click on **Run** to run the copy data.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image75.png)

16. Click on **Save and run** button so that pipeline will be save and
    run.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image76.png)

	![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image77.png)

18. After the pipeline executes successfully, go to the left‑hand
    navigation menu, select your workspace named
    **FabricMigrationLabXXX,** and then click on **Lakehouse**.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image78.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image79.png)

## Task 7: Delete the Resources

1.  To delete resources , type **Resource groups** in the Azure portal
    search bar, navigate and click on **Resource
    groups** under **Services**.

    ![A screenshot of a computer Description automatically
generated](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image80.png)

2.  In the Resource groups page, select your resource group.

3.  On the **Resource Group** home page, select all resources and then
    click **Delete**.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image81.png)

4.  In the **Delete Resources** pane that appears on the right side,
    navigate to **Enter “delete” to confirm deletion** field, then click
    on the **Delete** button

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image82.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image83.png)

5.  Go to your +++https://app.fabric.microsoft.com/+++ Microsoft Fabric
    workspace

6.  Select the **...** option under the workspace name and
    select **Workspace settings**.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image84.png)

7.  Select **General** and **Remove this workspace.**

    ![A screenshot of a computer AI-generated content may be
incorrect.](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image85.png)

1.  Click on **Delete** in the warning that pops up.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image86.png)

2.  Wait for a notification that the Workspace has been deleted, before
    proceeding to the next lab.

    ![](https://raw.githubusercontent.com/technofocus-pte/mgrtnmsfbrcdepth/refs/heads/main/Cloud%20slice/Labguides/Lab%203/media/image87.png)

**Summary**

In this lab, you successfully migrated data and basic pipeline
functionality from Azure Synapse Analytics to Microsoft Fabric. You
created and configured necessary resources in both platforms, accessed
and transformed data using Lakehouse and notebooks, and rebuilt
pipelines to enable data movement within Fabric.

This lab demonstrates how Microsoft Fabric simplifies data integration
and analytics by providing a unified platform, making it easier to
modernize existing Synapse-based solutions.
