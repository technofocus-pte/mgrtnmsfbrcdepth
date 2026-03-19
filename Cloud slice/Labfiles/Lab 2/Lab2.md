**Introduction**

This lab focuses on migrating SQL objects and data from **Azure Synapse
Analytics (Dedicated SQL Pool)** to **Microsoft Fabric Data Warehouse**.
It demonstrates how to create a warehouse in Fabric, establish a
connection to Synapse, and use pipelines to copy data into OneLake. The
lab also covers validating migrated data and scheduling pipelines for
automated data movement.

**Objective**

By the end of this lab, you will be able to:

- Create a **Data Warehouse** in Microsoft Fabric

- Establish a **linked connection** to Azure Synapse SQL

- Build and execute a **data pipeline** to copy data from Synapse to
  Fabric

- Load data into **Fabric Warehouse tables** using Copy Data activity

- Validate data migration using **SQL queries**

- Schedule pipelines for **automated execution**

## Task 1: Create a Warehouse

1.  Open your browser, navigate to the address bar, and type or paste
    the following URL: +++https://app.fabric.microsoft.com/+++ then
    press the **Enter** button.

2.  Create a new Warehouse by clicking on the **+New item** button in
    the navigation bar.

3.  Click on the "**Warehouse**" tile.

> ![](./media/image1.png)

3.  In the **New warehouse** dialog box,
    enter **+++sqlpoolmigrate+++** in the **Name** field, click on
    the **Create** button and open the new warehouse.

> ![](./media/image2.png)
>
> ![](./media/image3.png)

4.  Click on **Fabric_migrationXXXXX** workspace in the left-sided
    navigation bar ![](./media/image4.png)

## Task 2: Create Linked Connection to Synapse SQL

1.  In the **Fabric_migrationXXX** page, select +**New item**. Then,
    click **Pipeline** to view the full list of available items under
    Get data.

> ![](./media/image5.png)

2.  On the **New** **pipeline** dialog box, in the **Name** field, enter
    +++**sqlpool_migratepipeline+++** and click on
    the **Create** button.

> ![](./media/image6.png)

3.  On newly created pipeline, select **Copy data** dropdown and
    choose **Add copy data activity** option.

> ![](./media/image7.png)

4.  With the **copy data** being selected, navigate to **Source** tab.

> ![](./media/image8.png)

5.  Select the **Connection** dropdown and select **Browse all** option.

> ![](./media/image9.png)

6.  Select **+ New** from the left pane![](./media/image10.png)

7.  From the data source options, select **Azure Synapse Analytics (SQL
    DW)** to begin the connection setup.

> ![](./media/image11.png)

1.  Select **New Connection → Azure Synapse Analytics (SQL)**

2.  Enter details:

    - **Server name:**
      \<your-synapse-sql-endpoint\>.sql.azuresynapse.net

    - **Database:** your dedicated sql pool

    - **Authentication:** Basic

    - **Usename:** sqladmin

    - **Password:** password321!

> ![](./media/image12.png)

8.  Open the **Table** dropdown and select the **fabric_employee**
    table.

![](./media/image13.png)

![](./media/image14.png)

9.  Now, navigate to **destination** tab.

> ![](./media/image15.png)

10. Click **Destination** tab

> ![](./media/image16.png)

11. On choose a destination window, select **OneLake catalog** from the
    left pane and select the **sqlpoolmigrate**.

> ![](./media/image17.png)
>
> ![](./media/image18.png)

12. Under the Destination tab, select the **Auto create** *table* option
    and specify the table as **dbo.employee** for the data load.

> ![](./media/image19.png)

8.  Click on **Run** to run the copy data.

> ![](./media/image20.png)

9.  Click on **Save and run** button so that pipeline will be save and
    run.

> ![](./media/image21.png)
>
> ![](./media/image22.png)
>
> ![](./media/image23.png)

13. After the successful execution of the pipeline, go to your SQL
    analytics endpoint Lakehouse and open the explorer to see the
    imported data.

> ![](./media/image24.png)

![](./media/image25.png)

14. Select **New SQL query** to write your SQL statements.

![](./media/image26.png)

15. Paste the code as shown in the image below, then click the **Play**
    icon to run it and observe the counts in the output.

**SELECT COUNT(\*) FROM dbo.employee;**

![](./media/image27.png)

SELECT TOP 10 \* FROM dbo.employee;

![](./media/image28.png)

16. Compare row counts with Synapse: go back to the Synapse workspace,
    navigate to **Develop**, and select **SQL script**.

![](./media/image29.png)

17. Ensure that the SQL script is connected to the **SQL dedicated
    pool** by selecting it from both the ‘Connect to’ dropdown and the
    ‘Use database’ dropdown, as highlighted in the image

![](./media/image30.png)

18. Enter the following code into the editor and click **Run** to
    execute it

![](./media/image31.png)

SQL

> SELECT COUNT(\*) FROM dbo.Customer;

![](./media/image32.png)

## Task 3: Schedule the Pipeline

1.  Now, click on **Fabric_migration -XX** on the left-sided navigation
    pane and select **sql_migratepipeline**

> ![](./media/image33.png)

2.  Click **Schedule**

> ![](./media/image34.png)

19. Select **+ Add schedule** and configure the schedule as required
    then select **Save** and close the **Schedule** panel.

> **Note**: The example here schedules the pipeline to execute daily at
> 8:00 PM until the end of the year.
>
> ![](./media/image35.png)
>
> ![](./media/image36.png)

## Task 4: Delete the Resources

1.  To delete resources , type **Resource groups** in the Azure portal
    search bar, navigate and click on **Resource
    groups** under **Services**.

> ![A screenshot of a computer Description automatically
> generated](./media/image37.png)

2.  In the Resource groups page, select your resource group.

3.  On the **Resource Group** home page, select all resources and then
    click **Delete**.

![](./media/image38.png)

4.  In the **Delete Resources** pane that appears on the right side,
    navigate to **Enter “delete” to confirm deletion** field, then click
    on the **Delete** button

![](./media/image39.png)

![](./media/image40.png)

5.  Go to your +++ https://app.fabric.microsoft.com/+++ Microsoft Fabric
    workspace

6.  Select the **...** option under the workspace name and
    select **Workspace settings**.

> ![](./media/image41.png)

7.  Select **General** and **Remove this workspace.**

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image42.png)

1.  Click on **Delete** in the warning that pops up.

> ![](./media/image43.png)

**Summary**

In this lab, you successfully migrated data from Azure Synapse Analytics
to Microsoft Fabric Data Warehouse. You created a new warehouse,
configured a pipeline with a Synapse SQL source, and loaded data into
Fabric using OneLake. After executing the pipeline, you validated the
data by comparing row counts between Synapse and Fabric. Finally, you
scheduled the pipeline to automate recurring data transfers, ensuring a
streamlined and scalable migration process.
