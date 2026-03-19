**Introduction**

This lab provides a comprehensive, hands‑on experience to help you
understand and execute the end‑to‑end process of migrating data and
metadata from **Azure Synapse Analytics dedicated SQL pools** to the
**Microsoft Fabric Data Warehouse**. As organizations modernize their
analytics platforms, Microsoft Fabric offers a unified, scalable, and
fully integrated environment that simplifies data engineering,
warehousing, real‑time analytics, and business intelligence.

Throughout this lab, you will create and configure Azure Synapse
components, ingest sample datasets, integrate them through pipelines,
set up a new Fabric workspace, and use the **Fabric Migration
Assistant** to seamlessly migrate metadata and data into the Fabric Data
Warehouse. You will also learn to reroute dependent analytical tools and
processes to the migrated environment, ensuring business continuity.

**Objectives**

By the end of this lab, you will be able to:

1.  **Set up Azure Synapse Analytics resources**

    - Create a Synapse workspace and dedicated SQL pool

    - Configure security and connect to storage accounts

2.  **Load and manage data in Azure Synapse**

    - Upload sample datasets into Azure Data Lake Storage Gen2

    - Understand primary storage paths and metadata properties

3.  **Build and run data pipelines**

    - Create data integration pipelines using Synapse Studio

    - Copy and transform data into Synapse SQL pools

4.  **Create and configure a Microsoft Fabric workspace**

    - Set up a Fabric workspace using the Fabric portal

    - Prepare it for migration activities

5.  **Migrate metadata using Migration Assistant**

    - Upload and translate DACPAC metadata

    - Review automatically adjusted objects and resolve migration issues

6.  **Migrate data into Fabric Data Warehouse**

    - Configure and run Fabric Data Factory copy jobs

    - Validate successful data movement

7.  **Fix incompatible scripts**

    - Use Migration Assistant and Copilot suggestions to correct
      unsupported T‑SQL

8.  **Reroute application and reporting connections**

    - Identify existing connections to Synapse

    - Update Power BI, ETL, and other systems to point to Fabric
      Warehouse

## Task 0: Understand the VM and the credentials

In this task, we will identify and understand the credentials that we
will be using throughout the lab.

1.  **Instructions** tab hold the lab guide with the instructions to be
    followed throughout the lab.

2.  **Resources** tab has got the credentials that will be needed for
    executing the lab.

    - **URL** – URL to the Azure portal

    - **Subscription** – This is the ID of the subscription assigned to
      you

    - **Username** – The user id with which you need to login to the
      Azure services.

    - **Password** – Password to the Azure login. Let us call this
      Username and password as Azure login credentials. We will use
      these creds wherever we mention Azure login credentials.

    - **Resource Group** – The **Resource group** assigned to you.

\[!Alert\] **Important:** Make sure you create all your resources under
this Resource group

> ![](./media/image1.png)

3.  **Help** tab holds the Support information. The **ID** value here is
    the **Lab instance ID** which will be used during the lab execution.

> ![](./media/image2.png)

## Task 1: Create a Synapse workspace in the Azure portal

1.  Open a browser go to +++https://portal.azure.com+++ and sign in with
    your cloud slice account below.

> Username: <+++@lab.CloudPortalCredential>(User1).Username+++
>
> Password: <+++@lab.CloudPortalCredential(User1).Password>+++
>
> ![A screenshot of a computer Description automatically
> generated](./media/image3.png)
>
> ![A screenshot of a login box Description automatically
> generated](./media/image4.png)

2.  In the search bar enter **Synapse** and select **Azure Synapse
    Analytics.** Search for **Synapse Analytics**.

> ![](./media/image5.png)

3.  Click **Create**.

> ![](./media/image6.png)

4.  Enter below details to create resource group and then click
    on **OK**.

    1.  **Subscription**: Select the assigned subscription

    2.  **Resource Group**: Select the assigned Resource group

    3.  **Managed Resource group:**  Leave this blank.

    4.  **Workspace name**: fabric-synapseXXXX (XXXXX can be unique
        number)

    5.  **Region**: Select the assigned **region**

- **Select Data Lake Storage Gen2 account:** Create new

  1.  **Account name**fabricsynapsegen2XXXX (XXXX can be unique number)

  2.  Click **OK**

![](./media/image7.png)

![](./media/image8.png)

5.  Enter below details and then click on **Next:Security**.

**File System Name**: Create
New: [**synapsefileXX**](urn:gd:lg:a:send-vm-keys)(XX can be unique
number) and click **OK**.

![](./media/image9.png)

![](./media/image10.png)

6.  Configure the **Security** settings by selecting **both Microsoft
    Entra ID and SQL authentication**, then provide a valid **SQL admin
    username: sqladmin** and **password: password321!** to enable secure
    access to the Azure Synapse workspace.

7.  Click **Review + create**

> ![](./media/image11.png)

8.  In the **Review + submit** tab, once the Validation is Passed, click
    on the **Create** button.

> ![](./media/image12.png)

9.  This deployment may take a few minutes.

![](./media/image13.png)

10. Click on **Go to resource group** button.

> ![](./media/image14.png)

11. Click on your **Synapse workspace**.

![](./media/image15.png)

## Task 2: Create a dedicated SQL pool

1.  In Open Synapse Studio box, click on **Open** to launch your Azure
    Synapse studio.

> ![](./media/image16.png)

This launches the Synapse Studio interface.

2.  In Synapse Studio, select **Manage** (left pane).

> ![](./media/image17.png)

3.  In Synapse Studio, on the left-side pane, select **Manage** \> **SQL
    pools** under **Analytics pools** and then click on **New.**

![](./media/image18.png)

4.  **Configure SQL Pool**

- **SQL pool name:** +++sql dedicated pool+++

- **Performance level:** Choose DW100c (or any required level for
  training)

- Click **Review + Create → Create**.

![](./media/image19.png)

5.  In the **Review + submit** tab, once the Validation is Passed, click
    on the **Create** button.

![](./media/image20.png)

![](./media/image21.png)

6.  Go to **Manage → SQL pools** and confirm the Dedicated SQL Pool
    shows **Online**

![](./media/image22.png)

**mportant**: A dedicated SQL pool consumes billable resources as long
as it's active. You can pause the pool later to reduce costs. Please
Pause it if you not performing labs for the day or even for 30 minutes
to save your credits.

Make sure to resume your SQLPool before you start your labs.

7.  In the Overview section of the Synapse workspace home page, copy the
    **Dedicated SQL endpoint and Dedicated SQL pool**, and save them in
    a notepad.

![](./media/image23.png)

## Task 3: Place sample data into the primary storage account

We are going to use a small 100K row sample dataset of NYX Taxi Cab data
for many examples in this getting started guide. We begin by placing it
in the primary storage account you created for the workspace.

1.  In Synapse Studio, navigate to the **Data Hub**. Select **Linked**.

![](./media/image24.png)

2.  Under the category **Azure Data Lake Storage Gen2** you'll see an
    item with your workspace name like **fabric-synapseXXXXX ( Primary
    -- asastorageaccount01(your storageaccount)**

![](./media/image25.png)

3.  Select the container named **synapsefile (Primary)**.

![](./media/image26.png)

4.  Select **Upload** and
    browse ***NYCTripSmall.parquet*** from **C:\Labfiles** and then
    click on **Upload** button.

![](./media/image27.png)

![](./media/image28.png)

> ![](./media/image29.png)

5.  Click on **Upload** button.

> ![](./media/image30.png)

6.  The **paraquest** file uploaded successfully.

> ![](./media/image31.png)

7.  Repeat the same steps to upload the remaining **DimDate.csv,
    Dimension_Employee.csv** files![](./media/image32.png)

> ![](./media/image33.png)
>
> ![](./media/image34.png)
>
> ![](./media/image35.png)
>
> ![](./media/image36.png)
>
> ![](./media/image37.png)
>
> ![](./media/image38.png)
>
> ![](./media/image39.png)

8.  Right click on it and then click on **NYCTripSmall.parquet** and
    select **Properties**.

> ![](./media/image40.png)

9.  In Properties page, copy URL and ABFSS path and save it in notepad
    to use them in further labs.

**Note**: URL and AFSS path might be different for your account.

![](./media/image41.png)

URL: https://fabricsynapsegen2.dfs.core.windows.net/synapsefile/NYCTripSmall.parquet

**ABFSS Path:
abfss://synapsefile@fabricsynapsegen2.dfs.core.windows.net/NYCTripSmall.parquet**

## Task 4: Integrate with pipelines

1.  **In Synapse Studio,** click on** Integrate hub.**

![](./media/image42.png)

2.  Select** + Pipeline **to create a new pipeline**.**

![](./media/image43.png)

3.  Under **Activities**, expand the **Move and transform** , and drag
    a **Copy data** object into the canvas.

![](./media/image44.png)

> ![](./media/image45.png)

4.  On the **Source** page, select **+** **New**

![](./media/image46.png)

> ![](./media/image47.png)

5.  In **New integration dataset**, select the **Azure Data Lake Storage
    Gen2** tile.

![](./media/image48.png)

6.  Select the **DelimitedText** tile and click **Continue**.

> ![](./media/image49.png)

7.  In **Set properties**, click **+ New** under **Linked service** to
    create a new connection to the storage account

> ![](./media/image50.png)

8.  In **New linked service**, provide a name, select your **Azure
    subscription** and **Storage account**, then click **Test
    connection** and **Create** after a successful validation.

> ![](./media/image51.png)

9.  Browse and select the **folder**, then choose the required file.

> ![](./media/image52.png)

10. Select the **DimDate.csv** file and click **OK**.

> ![](./media/image53.png)

11. In the Synapse pipeline, the user selects the **Sink** tab within
    the Copy Data activity to configure the destination settings for the
    dataset.

![](./media/image54.png)

12. On the **Source** page, select **+** **New**

![](./media/image55.png)

13. In **New integration dataset**, select the **Azure Synapse
    Analytics** tile.

> ![](./media/image56.png)

14. In the New Linked Service setup, the **Azure subscription, server
    name, and database name** fields are selected to configure the
    connection to the Synapse SQL dedicated pool

> ![](./media/image57.png)

15. Enter User name as +++**sqladmin**+++ and password as
    +++**password321!**+++. Click **Test connection**. If successful,
    click **Continue**.

> ![](./media/image58.png)

16. In the Set Properties window, the table name is manually specified
    as **dbo.fabric**, the Import schema option is set to ‘None’, and
    the configuration is confirmed by selecting **OK**

![](./media/image59.png)

17. Select the **Auto create table** option in the Sink settings and
    then click on the **Mapping** tab to proceed.

![](./media/image60.png)

18. Click **Import schemas** to automatically detect and load the file
    structure.

![](./media/image61.png)

19. Click **Debug**

![](./media/image62.png)

> ![](./media/image63.png)

20. Repeat the same steps as before, then click on the **Source** tab
    and select **Open** to view the source dataset configuration

> ![](./media/image64.png)

21. Click **Browse** folder

![](./media/image65.png)

22. Select the **Dimension_Employee.csv** file and click **OK**.

![](./media/image66.png)

23. Click **Pipeline**

![](./media/image67.png)

24. Click on the **Sink** tab and then select **Open** to view the sink
    dataset configuration.

![](./media/image68.png)

25. In the **sink** dataset configuration, enter the schema as **dbo**
    and specify the table name as **fabric_employee**, then return to
    **Pipeline 1** to continue with the setup.

![](./media/image69.png)

26. Click **Mapping**

![](./media/image70.png)

27. Click **Import schema**

> ![](./media/image71.png)

28. Click **Debug** to test and validate the pipeline execution.

![](./media/image72.png)

> ![](./media/image73.png)

29. **In Synapse Studio,** click on** Data hub.**

> ![](./media/image74.png)

30. Make sure **files** deployed successfully

![](./media/image75.png)

## Task 5: Create a Fabric workspace

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

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image76.png)

[TABLE]

3.  On the **Create a workspace** pane that appears to the right, enter
    the following details, and then click **Apply**.

> ![](./media/image77.png)
>
> ![](./media/image78.png)

4.  The Workspace is now created.

> ![](./media/image79.png)

## Task 6: Copy metadata

1.  In your Fabric workspace, select the **Migrate** button on the item
    action deck.

> ![](./media/image80.png)

2.  In the **Migrate to Fabric** source menu, under **Migrate to
    Fabric**, select the **Azure Synapse Analytics dedicated SQL
    pool** tile.

> ![](./media/image81.png)

3.  On the **Overview**, review the information and select **Next**.

> ![](./media/image82.png)

4.  Click **Choose file**

> ![](./media/image83.png)

5.  Browse to **C:\LabFiles**, select the **AdventureWorks.DACPAC**
    file, and click **Open**.

> ![](./media/image84.png)

6.  When the upload is complete, select **Next**.

![](./media/image85.png)

> In the **Set the destination** page, enter the **Fabric workspace
> name** and specify a new warehouse item (e.g.,
> **Migration_Warehouse**) as the target, then click **Next**.
>
> ![](./media/image86.png)

7.  Review your inputs and select **Migrate**. A new warehouse item is
    created and the metadata migration begins.

> ![](./media/image87.png)

** Note:** When using the Migration Assistant, the new warehouse
has **case insensitive collation**, regardless of the [**default
warehouse collation
setting**](https://learn.microsoft.com/en-us/fabric/data-warehouse/collation).

![Screenshot from the Fabric portal of the Review page of the Migration
Assistant. The source is a DACPAC file and the Destination is a new
warehouse item named AdventureWorks.](./media/image88.png)

During this step, the Migration Assistant translates T-SQL metadata to
supported T-SQL syntax in Fabric data warehouse. Once the metadata
migration is complete, the Migration assistant opens. You can access the
Migration Assistant at any time using the **Migration** button in the
Home tab of the warehouse ribbon.

![](./media/image89.png)

8.  Review the metadata migration summary in the Migration Assistant.
    You'll see the count of migrated objects and the objects that need
    to be fixed before they can be migrated.

> ![](./media/image90.png)

9.  Select **Show migrated objects** to expand the section and see a
    list of objects that have been successfully migrated to your Fabric
    warehouse.

> ![](./media/image90.png)

![](./media/image91.png)

The **State** column indicates if the object's metadata was adjusted
during the translation to be supported in Fabric Warehouse. For example,
you might see that certain column datatypes or T-SQL language constructs
are automatically converted to the ones that are supported in Fabric.
The **Details** column shows the information about the adjustments that
were made to the objects.

10. Select any object to see the adjustments that were made during
    migration.

11. Open the metadata migration summary in full screen view for better
    readability. Apply filters to view specific object types.

> ![](./media/image92.png)
>
> ![](./media/image93.png)

![Screenshot of the full screen view of the Migration Assistant's
metadata migration summary of migrated objects.](./media/image94.png)

12. Optionally, select the **Export** menu to download a migration
    summary as an Excel file or a CSV.

    - The downloaded Excel file is a fully structured workbook with two
      worksheets: **Migrated Objects** and **Objects To Fix**. It is
      MIP-compliant and aligned with your organization's sensitivity
      labels.

    - The CSV is lightweight and tool-friendly.

![](./media/image95.png)

13. Select the exported file to review the **CSV**
    data.![](./media/image96.png)

> ![](./media/image97.png)

14. Each exported file provides a structured, comprehensive view of your
    migration results, including:

[TABLE]

## Task 7: Fix problems using Migration Assistant

Some database object metadata might fail to migrate. Commonly, this is
because the Migration Assistant couldn't translate the T-SQL metadata
into those that are supported in a Fabric warehouse or the translated
code failed to apply to T-SQL.

Let's fix these scripts with help from the Migration Assistant.

1.  Select the **Fix problems** step in the Migration Assistant to see
    the scripts that failed to migrate.

> ![](./media/image98.png)

2.  Select a database object that failed to migrate. A new query opens
    under the **Shared queries** in the **Explorer**. This new query
    shows the metadata definition and the adjustments that were made to
    it as automatic comments added to the T-SQL code.

> ![](./media/image99.png)

3.  Review the comments in the beginning of the script to see the
    adjustments that were made to the script.

> ![](./media/image100.png)

4.  Review and fix the broken scripts using the error information and
    documentation.

5.  To use Copilot for AI-powered assistance in fixing the errors,
    select **Fix query errors** in the **Suggested action** section.
    Copilot updates the script with suggestions. Mistakes can happen as
    Copilot uses AI, so verify code suggestions and make any adjustments
    you need.

> ![](./media/image101.png)

15. Select **Run** to validate and create the object.

> ![](./media/image102.png)

16. The next script to be fixed opens.

> ![](./media/image103.png)

17. Continue to fix the rest of the scripts. You can choose to skip
    fixing scripts that you don't need during this step.

&nbsp;

6.  When all desired metadata is ready for migration, select the back
    button in the **Fix problems** pane to return the top-level view of
    the Migration assistant. Check the **2. Fix problems** step in the
    Migration assistant.

> ![](./media/image104.png)

## Task 8: Copy data using Migration Assistant

Copy data helps with migrating data used by the objects you migrate. You
can use a [Fabric Data Factory copy
job](https://learn.microsoft.com/en-us/fabric/data-factory/create-copy-job) to
do it manually, or follow these steps for the copy job integration in
the Migration assistant.

1.  Select the **Copy data** step in the Migration Assistant.

> ![](./media/image105.png)

2.  Select **Use a copy job** button.

> ![](./media/image106.png)

3.  Assign a name to the new job as **migrate_copyjob**, then
    select **Create**.

> ![](./media/image107.png)

4.  In **Connect to data source** page, provide **Connection
    credentials** the source **Azure Synapse Analytics (SQL DW)**
    warehouse. Select **Next**.

> ![](./media/image108.png)

5.  In Connection settings tab enter the below detail and click on
    Connect button

[TABLE]

> ![](./media/image109.png)

6.  In the **Choose data** page, select the tables you want to migrate.
    The object metadata should already exist in the target warehouse.
    Select **Next**.

![](./media/image110.png)

7.  Click **Next**

![](./media/image111.png)

8.  In the **Map to destination** page, configure each table's column
    mappings. Select **Next**.

> ![](./media/image112.png)

9.  Review the job summary. Select **Save + Run**.

> ![](./media/image113.png)

10. Once copy job is complete, check the **3. Copy data** step in the
    Migration assistant. Select the back button on the top to return the
    top-level view of the Migration assistant.

> ![](./media/image114.png)
>
> ![](./media/image115.png)

## Task 9: Reroute connections

In the final step, the data loading/reporting platforms that are
connected to your source need to be reconnected to your new Fabric
warehouse.

1.  Identify connections on your existing source warehouse.

    - For example, in Azure Synapse Analytics dedicated SQL pools, you
      can find session information including source application, who is
      connected in, where the connection is coming from, and if its
      using Microsoft Entra ID or SQL authentication:

2.  Go to your Synapse workspace

3.  In Synapse Studio, navigate to the **Develop** hub, click
    the **+** button to add new resource, then select **SQL
    script**.![](./media/image116.png)

4.  Ensure that the SQL script is connected to the **SQL dedicated
    pool** by selecting it from both the ‘Connect to’ dropdown and the
    ‘Use database’ dropdown, as highlighted in the image

![](./media/image117.png)

5.   Enter the following code into the editor and click **Run** to
    execute it

**SQL**

SELECT DISTINCT CASE

WHEN len(tt) = 0

THEN app_name

ELSE tt

END AS application_name

,login_name

,ip_address

FROM (

SELECT DISTINCT app_name

,substring(client_id, 0, CHARINDEX(':', ISNULL(client_id,
'0.0.0.0:123'))) AS ip_address

,login_name

,isnull(substring(app_name, 0, CHARINDEX('-', ISNULL(app_name, '-'))),
'h') AS tt

FROM sys.dm_pdw_exec_sessions

) AS a;

![](./media/image118.png)

6.  After running the SQL script, the query results are displayed in the
    table view, showing the application name, login name, and IP address
    returned by the query

> ![](./media/image119.png)

7.  Update the connections for data loading (ETL/ELT) platforms to point
    to your Fabric warehouse.

    - For Power BI/Fabric pipelines:

    &nbsp;

    - Use the [List Connections REST
      API](https://learn.microsoft.com/en-us/rest/api/fabric/core/connections/list-connections?tabs=HTTP) to
      find connections to your old data source, the Azure Synapse
      Analytics dedicated SQL pool.

    &nbsp;

    - Update the connections to the new Fabric data warehouse
      using **Manage Connections and Gateways** experience under
      the **Settings** gear.

8.  Once complete, check the **Reroute connections** step in the
    Migration assistant.

![](./media/image120.png)

Congratulations! You're now ready to start using the warehouse.

![](./media/image121.png)

![](./media/image122.png)

**Important**: A dedicated SQL pool consumes billable resources as long
as it's active. Please ensure that you pause the pool even if you are
taking a small break from the lab execution. If the pool is in active
state for more than 40 minutes and is not being used, your account might
get disabled

**Important:** Proceed to continue working on Lab 2

**Summary**

In this lab, you successfully performed a full migration journey from
**Azure Synapse Analytics dedicated SQL pools** to **Microsoft Fabric
Data Warehouse**. You began by configuring Azure and Synapse resources,
uploading datasets, and creating data pipelines. You then created a
Microsoft Fabric workspace and used the **Migration Assistant** to
translate and migrate metadata, fix compatibility issues, and copy data
into the new warehouse.

Finally, you learned how to reroute external applications—such as Power
BI, pipelines, and ETL tools—to the Fabric environment to ensure smooth
operational transition. Completion of this lab equips you with essential
skills needed for modernizing analytical workloads using Microsoft
Fabric.
