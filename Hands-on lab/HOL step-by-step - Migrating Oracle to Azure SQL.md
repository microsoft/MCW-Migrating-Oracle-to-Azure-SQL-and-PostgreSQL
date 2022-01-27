![Microsoft Cloud Workshops](https://github.com/Microsoft/MCW-Template-Cloud-Workshop/raw/main/Media/ms-cloud-workshop.png "Microsoft Cloud Workshops")

<div class="MCWHeader1">
Migrating Oracle to Azure SQL
</div>

<div class="MCWHeader2">
Hands-on lab step-by-step
</div>

<div class="MCWHeader3">
September 2021
</div>

Information in this document, including URL and other Internet Web site references, is subject to change without notice. Unless otherwise noted, the example companies, organizations, products, domain names, e-mail addresses, logos, people, places, and events depicted herein are fictitious, and no association with any real company, organization, product, domain name, e-mail address, logo, person, place or event is intended or should be inferred. Complying with all applicable copyright laws is the responsibility of the user. Without limiting the rights under copyright, no part of this document may be reproduced, stored in or introduced into a retrieval system, or transmitted in any form or by any means (electronic, mechanical, photocopying, recording, or otherwise), or for any purpose, without the express written permission of Microsoft Corporation.

Microsoft may have patents, patent applications, trademarks, copyrights, or other intellectual property rights covering subject matter in this document. Except as expressly provided in any written license agreement from Microsoft, the furnishing of this document does not give you any license to these patents, trademarks, copyrights, or other intellectual property.

The names of manufacturers, products, or URLs are provided for informational purposes only and Microsoft makes no representations and warranties, either expressed, implied, or statutory, regarding these manufacturers or the use of the products with any Microsoft technologies. The inclusion of a manufacturer or product does not imply endorsement of Microsoft of the manufacturer or product. Links may be provided to third party sites. Such sites are not under the control of Microsoft and Microsoft is not responsible for the contents of any linked site or any link contained in a linked site, or any changes or updates to such sites. Microsoft is not responsible for webcasting or any other form of transmission received from any linked site. Microsoft is providing these links to you only as a convenience, and the inclusion of any link does not imply endorsement of Microsoft of the site or the products contained therein.

© 2020 Microsoft Corporation. All rights reserved.

Microsoft and the trademarks listed at <https://www.microsoft.com/en-us/legal/intellectualproperty/Trademarks/Usage/General.aspx> are trademarks of the Microsoft group of companies. All other trademarks are property of their respective owners.

**Contents**

- [Migrating Oracle to Azure SQL hands-on lab step-by-step](#migratingoracletoazuresql-hands-on-lab-step-by-step)
  - [Abstract and learning objectives](#abstract-and-learning-objectives)
  - [Overview](#overview)
  - [Solution architecture](#solution-architecture)
  - [Requirements](#requirements)
  - [Exercise 1: Setup Oracle 21c Express Edition](#exercise-1-setup-oracle-21c-express-edition)
    - [Task 1: Create the Northwind database in Oracle 21c XE](#task-1-create-the-northwind-database-in-oracle-21c-xe)
    - [Task 2: Configure the Starter Application to use Oracle](#task-2-configure-the-starter-application-to-use-oracle)
  - [Exercise 2: Assess the Oracle 21c Database before Migrating to Azure SQL Database](#exercise-2-assess-the-oracle-21c-database-before-migrating-to-azure-sql-database)
    - [Task 1: Update Statistics and Identify Invalid Objects](#task-1-update-statistics-and-identify-invalid-objects)
  - [Exercise 3: Migrate the Oracle database to Azure SQL Database](#exercise-3-migrate-the-oracle-database-to-azure-sql-database)
    - [Task 1: Migrate the Oracle database to Azure SQL Database using SSMA](#task-1-migrate-the-oracle-database-to-azure-sql-database-using-ssma)
    - [Task 2: Additional SSMA Usage Details](#task-2-additional-ssma-usage-details)
  - [Exercise 4: Migrate the Application](#exercise-4-migrate-the-application)
    - [Task 1: Create new Entity Models against Azure SQL Database and Scaffold Views](#task-1-create-new-entity-models-against-azure-sql-database-and-scaffold-views)
    - [Task 2: Ensure Application Compatibility with the Stored Procedure](#task-2-ensure-application-compatibility-with-the-stored-procedure)
  - [Exercise 5: Configure SQL Server instances (Optional Homogenous Migration)](#exercise-5-configure-sql-server-instances-optional-homogenous-migration)
    - [Task 1: Connect to the SqlServer2008 VM](#task-1-connect-to-the-sqlserver2008-vm)
    - [Task 2: Install AdventureWorks sample database](#task-2-install-adventureworks-sample-database)
    - [Task 3: Update SQL Server settings using Configuration Manager](#task-3-update-sql-server-settings-using-configuration-manager)
  - [Exercise 6: Migrate SQL Server to Azure SQL Database using DMS (Optional Homogenous Migration)](#exercise-6-migrate-sql-server-to-azure-sql-database-using-dms-optional-homogenous-migration)
    - [Task 1: Assess the on-premises database](#task-1-assess-the-on-premises-database)
    - [Task 2: Migrate the database schema](#task-2-migrate-the-database-schema)
    - [Task 3: Create a migration project](#task-3-create-a-migration-project)
    - [Task 4: Run the migration](#task-4-run-the-migration)
    - [Task 5: Verify data migration](#task-5-verify-data-migration)
  - [Exercise 7: Post upgrade enhancement (Optional Homogenous Migration)](#exercise-7-post-upgrade-enhancement-optional-homogenous-migration)
    - [Task 1: Table compression](#task-1-table-compression)
    - [Task 2: Clustered ColumnStore index](#task-2-clustered-columnstore-index)
  - [After the hands-on lab](#after-the-hands-on-lab)
    - [Task 1: Delete the resource group](#task-1-delete-the-resource-group)

# Migrating Oracle to Azure SQL hands-on lab step-by-step

## Abstract and learning objectives

In this hands-on lab, you implement a proof of concept (POC) for conducting a site analysis for a customer to compare cost, performance, and level of effort required to migrate from Oracle to Azure SQL Database. You evaluate the dependent applications and reports that need to be updated and come up with a migration plan. Also, you help the customer take advantage of new features to improve performance and resiliency.

Optionally, you can also pursue a migration of a SQL Server 2008 R2 instance to Azure SQL Database for the customer's data warehouse. Again, you will leverage advanced features of Azure SQL Database to satisfy the customer's compliance and performance needs.

At the end of this hands-on lab, you will be better able to design and build a database migration plan and implement any required application changes associated with changing database technologies.

## Overview

Wide World Importers (WWI) has experienced significant growth in the last few years. As a result, the Oracle OLTP database that powers their vendor integrations and inventory and sales applications has proved inadequate. The CIO has read about the performance and security enhancements of Azure SQL Database. Before committing to an Azure migration, the CIO would like a PoC of the OLTP database migration and the necessary modifications to the ASP.NET Core inventory app.

In addition to predictable growth, they’ve had a substantial amount of growth in the data they store in their data warehouse. Their data warehouse is starting to show its age, slowing down during extract, transform, and load (ETL) operations and during critical queries. The data warehouse is running on SQL Server 2008 R2 Standard Edition. The CIO is excited about the potential performance improvements offered by Azure SQL Database, such as clustered ColumnStore indexes. She is also hoping that table compression can improve performance and backup times.

## Solution architecture

Below is a diagram of the solution architecture you build in this lab. Please study this carefully, so you understand the whole of the solution as you are working on the various components.

![This solution diagram is divided into Microsoft Azure and on-premises. Microsoft Azure includes two Azure SQL Database instances and the Azure Data Factory SSIS Integration Runtime to execute SSIS packages hosted in Azure Files. It also includes Azure Analysis Services, Power BI, and SSRS on an Azure VM as a lift-and-shift alternative to Power BI for their reporting needs. On-premises includes the following elements: API App for vendor connections; Web App for Internet Sales Transactions; ASP.NET Core App for inventory management; and Excel for reporting.](./media/new-suggested-architecture.png "Preferred Solution diagram")

The Oracle XE database supporting the application will be migrated to an Azure SQL Database instance using SQL Server Migration Assistant (SSMA) 8.x for Oracle. Once the Oracle database has been migrated, the Northwind MVC application will be updated, so it targets Azure SQL Database instead of Oracle. The entity models are updated against Azure SQL Database, new controllers and views are scaffolded, and code updates are made to use the new Entity Framework Core context. Corrections to stored procedures are made due to differences in how stored procedures are accessed in Oracle PL/SQL versus T-SQL.

For the homogenous migration, the solution begins with using the Microsoft Data Migration Assistant to assess what potential issues might exist for upgrading the database to Azure SQL Database. After correcting any problems, the SQL Server 2008 R2 database is migrated to Azure SQL Database using the Azure Database Migration Service. Two features of Azure SQL Database, Table Compression and ColumnStore Index, will be applied to demonstrate value and performance improvements from the upgrade. For the ColumnStore Index, a new table based on the existing FactResellerSales table will be created, and a ColumnStore index applied. 

## Requirements

- Microsoft Azure subscription must be pay-as-you-go or MSDN.
  - Trial subscriptions will not work.
- A virtual machine configured with Visual Studio 2019 Community edition.

## Exercise 1: Setup Oracle 21c Express Edition

Duration: 45 minutes

In this exercise, you will load a sample database supporting the application. Ensure that you installed Oracle XE, Oracle Data Access Components, and Oracle SQL Developer, as detailed in the [Before the Hands-on Lab document](Before%20the%20HOL%20-%20Migrating%20Oracle%20to%20Azure%20SQL%20and%20PostgreSQL.md).

### Task 1: Create the Northwind database in Oracle 21c XE

WWI has provided you with a copy of their application, including a database script to create their Oracle database. They have asked that you use this as a starting point for migrating their database and application to Azure SQL DB. In this task, you will create a connection to the Oracle database on your Lab VM.

1. In a web browser on LabVM, download a copy of the [Migrating Oracle to  Azure SQL and PostgreSQL upgrade and migration MCW repo](https://github.com/microsoft/MCW-Migrating-Oracle-to-Azure-SQL-and-PostgreSQL/archive/refs/heads/master.zip).

2. Unzip the contents to **C:\handsonlab**.

3. Launch SQL Developer from the `C:\Tools\sqldeveloper` path from earlier. In the **Database Connection** window, select **Create a Connection Manually**.

   ![Manual connection creation in Oracle SQL Developer.](./media/create-connection-sql-developer.png "SQL Developer add connection manually")

4. Provide the following parameters to the **New / Select Database Connection** window. Select **Connect** when you are complete.

   - **Name**: Northwind
   - **Username**: system
   - **Password**: Password set in the ARM template
   - Keep the **Details** at their defaults

   ![Northwind connection in SQL Developer.](./media/new-oracle-connection-sqldeveloper.png "Northwind connection")

5. Once the connection completes, select the **Open File** icon (1). Navigate to `C:\handsonlab\MCW-Migrating-Oracle-to-Azure-SQL-and-PostgreSQL-master\Hands-on lab\lab-files\starter-project\Oracle Scripts\1.northwind.oracle.schema`. Then, execute the DDL statements (2).

   ![Execute schema creation script in SQL Developer.](./media/execute-first-northwind-sql-script.png "Schema creation script")

6. Right-click the **Northwind** connection and select **Properties**. Then, edit the **Username** to `NW`, and the **Password** to `oracledemo123`. Select **Connect**. Note that you may be asked to enter the password again.

7. In the Open File dialog, navigate to `C:\handsonlab\MCW-Migrating-Oracle-to-Azure-SQL-and-PostgreSQL-master\Hands-on lab\lab-files\starter-project\Oracle Scripts`, select the file `2.northwind.oracle.tables.views.sql`, and then select **Open**.

8. As you did previously, run the script. Note that SQL Developer provides an output pane to view any errors.

   ![Script output of the second Northwind database script.](./media/northwind-script-2-output.png "SQL Developer output pane")

9. Repeat steps 7 - 8, replacing the file name in step 7 with each of the following:

    - `3.northwind.oracle.packages.sql`

    - `4.northwind.oracle.sps.sql`

      - During the Execute script step for this file, you will need to execute each CREATE OR REPLACE statement independently.

      - Using your mouse, select the first statement, starting with CREATE and going to END. Then, run the selection, as highlighted in the image.

      ![The first statement between CREATE and END is highlighted, along with the selection execution button.](./media/sqldeveloper-execute-first-query.png "Select and execute the first statement")

      - Repeat this for each of the remaining CREATE OR REPLACE... END; blocks in the script file (there are 7 more to execute, for 8 total).

    - `5.northwind.oracle.seed.sql`

      > **Important**: This query can take several minutes to run, so make sure you wait until you see the **Commit complete** message in the output window before executing the next file.

10. After you finish running these scripts, validate that the database objects were created. The image below demonstrates the Tables and the Views created by the script.

    ![Presenting the tables and views generated by the Oracle scripts.](./media/views-table-validation.png "Oracle tables and views")

### Task 2: Configure the Starter Application to use Oracle

In this task, you will add the necessary configuration to the `NorthwindMVC` solution to connect to the Oracle database you created in the previous task.

1. On your LabVM, navigate to `C:\handsonlab\MCW-Migrating-Oracle-to-Azure-SQL-and-PostgreSQL\Hands-on lab\lab-files\starter-project`. Launch `NorthwindMVC.sln`.

   ![Launch NorthwindMVC.sln from the downloaded lab files.](./media/launch-northwind-solution.png "NorthwindMVC Visual Studio 2019 Solution")

2. If you are prompted to choose a particular version of Visual Studio, select **Visual Studio 2019** and continue.

3. As this is your first time opening Visual Studio 2019 on the LabVM, you will be prompted to enter the email address of your Visual Studio account. Enter it and proceed to the following steps.

4. In Visual Studio on your LabVM, select **Build** from the menu, then select **Build Solution**.

   ![Build Solution is highlighted in the Build menu in Visual Studio.](./media/visual-studio-menu-build-build-solution.png "Select Build Solution")

5. Open the `appsettings.json` file in the `NorthwindMVC` project by double-clicking the file in the Solution Explorer, on the right-hand side in Visual Studio.

6. In the `appsettings.json` file, locate the `ConnectionStrings` section, and verify the connection string named **OracleConnectionString** matches the values you have used in this hands-on lab:

   ```xml
   DATA SOURCE=localhost:1521/XE;PASSWORD=oracledemo123;USER ID=NW
   ```

   ```json
   "ConnectionStrings": {
      "OracleConnectionString": "DATA SOURCE=localhost:1521/XE;PASSWORD=oracledemo123;USER ID=NW"
   }
   ```

7. Run the solution by selecting the green **Start** button on the Visual Studio toolbar.

   ![Start is selected on the toolbar.](./media/visual-studio-toolbar-start.png "Run the solution")

8. You should see the Northwind Traders Dashboard load in your browser.

   ![The Northwind Traders Dashboard is visible in a browser.](./media/northwind-traders-dashboard.png "View the dashboard")

9.  Close the browser to stop debugging the application, and return to Visual Studio.

## Exercise 2: Assess the Oracle 21c Database before Migrating to Azure SQL Database

Duration: 15 minutes

In this exercise, you will prepare the existing Oracle database for its migration to Azure SQL DB. Preparation involves two main steps. The first step is to update the database statistics. Statistics about the database become outdated as data volumes and activity change over time. Second, you will need to identify invalid objects in the Oracle database to minimize disturbances to the migration.

### Task 1: Update Statistics and Identify Invalid Objects

1. Launch SQL Developer. Open the **Northwind** database connection.

2. Create a new SQL file using the **New** button (1). Select **Database Files** (2) and **SQL File** (3). Select **OK** (4). 

    ![Creating a new SQL script in Oracle SQL Developer](./media/creating-new-sql-file-sqldev.png "New SQL file")
   
3. Call the new SQL File `update-21c-stats.sql`. Save it in a location of your choice, such as the `Oracle Scripts` directory from earlier.

4. Now, you will populate the new file with the following statements. Run the file as you did when you populated database objects.

    ```sql
    -- 21c script
    EXECUTE DBMS_STATS.GATHER_SCHEMA_STATS(ownname => 'NW');
    EXECUTE DBMS_STATS.GATHER_DATABASE_STATS;
    EXECUTE DBMS_STATS.GATHER_DICTIONARY_STATS;
    ```

    >**Note**: This script can take over one minute to run. Ensure that you receive confirmation that the script has executed successfully.

5. Now, we will utilize a query that lists invalid database objects. It is recommended to fix any errors and compile the objects before starting the migration process. Create a new file named `show-invalid-objects.sql` and save it in the same directory. Run this query to find all of the invalid objects.

    ```sql
    SELECT owner, object_type, object_name
    FROM all_objects
    WHERE status = 'INVALID';
    ```

    >**Note**: You should not see any invalid objects.

## Exercise 3: Migrate the Oracle database to Azure SQL Database

Duration: 30 minutes

In this exercise, you will migrate the Oracle database to Azure SQL DB using SSMA.

### Task 1: Migrate the Oracle database to Azure SQL Database using SSMA

1. On your LabVM, launch **Microsoft SQL Server Migration Assistant for Oracle** from the Start Menu.

2. Select **File**, then **New Project...**

   ![File and New Project are highlighted in the SQL Server Migration Assistant for Oracle.](./media/ssma-menu-file-new-project.png "Select New Project")

3. In the New Project dialog, accept the default name and location, select **Azure SQL Database** for the Migrate To value, and select **OK**.

   ![In the New Project dialog box, Azure SQL DB is selected and highlighted in the Migration To box.](./media/ssma-new-project.png "New Project dialog box")

4. Select **Connect to Oracle** in the SSMA toolbar.

   ![Connect to Oracle is highlighted on the SSMA toolbar.](./media/ssma-toolbar-connect-to-oracle.png "Select Connect to Oracle")

5. In the Connect to Oracle dialog, enter the following:

   - **Provider**: Leave set to the default value, Oracle Data Provider for .NET.
   - **Mode**: Leave set to Standard mode.
   - **Server name**: localhost
   - **Server port**: Set to 1521.
   - **Oracle SID**: XE
   - **Username**: NW
   - **Password**: oracledemo123

   ![The information above is entered in the Connect to Oracle dialog box, and Connect is selected at the bottom.](./media/ssma-connect-to-oracle.png "Specify the settings")

   >**Note**: You can also connect to SSMA using a connection string through the **Mode** dropdown. Irrespective of how you connect to Oracle from SSMA, using the **Oracle Data Provider for .NET** is the best-practice technology.

6. Select **Connect**.

7. In the Filter objects dialog, uncheck **Load all user objects**. Then, select the **NW** schema. Note that the **Sys** and **System** schemas are automatically checked. 

   ![The NW schema is highlighted and checked in the Filter objects dialog. The System schema is checked, and all others are unchecked.](media/ssms-filter-objects.png "SSMA Filter objects")

   >**Note**: In production Oracle environments, you must ensure that you have sufficient permissions to run SSMA. See Microsoft's complete list [here.](https://docs.microsoft.com/sql/ssma/oracle/connecting-to-oracle-database-oracletosql)

8. In the Output window, you will see a message that the connection was established successfully, similar to the following:

   ![The successful connection message is highlighted in the Output window.](./media/ssma-connect-to-oracle-success.png "View the successful connection message")

9. Under Oracle Metadata Explorer, expand the localhost node, Schemas, and confirm you can see the NW schema, which will be the source for the migration.

   ![The NW schema is highlighted in Oracle Metadata Explorer.](./media/ssma-oracle-metadata-explorer-nw.png "Confirm the NW schema")

10. In the Oracle Metadata Explorer, check the box next to NW, expand the NW database, and uncheck **Packages**. Next, select NW to make sure it is selected in the tree.

    ![The NW schema is selected and highlighted in Oracle Metadata Explorer.](./media/ssma-oracle-metadata-explorer-nw-selected.png "Confirm the NW schema")

11. Right-click the **NW** schema. Select **Create Report**. SSMA will use the metadata it has compiled about objects in the **NW** schema to produce a useful report. SSMA also allows you to create reports at the object level.

12. Once the report is generated, expand the hierarchy on the left-hand side of the page. Select an object that is marked by an error, such as the `CUSTORDERSDETAILS` procedure. On the left side, observe the PL/SQL code, and on the right side, observe the T-SQL code generated by SSMA.
    
    ![CUSTORDERDETAILS stored procedure in the SSMA HTML report.](./media/custorderdetails-assessment.png "Stored procedure in SSMA HTML report")

13. You can use the report to navigate through the issues that SSMA encountered during the conversion. Use the error navigation tool highlighted in the **Source** tab.

    ![Error navigation tool in the SSMA HTML report.](./media/error-navigation-ssma.png "Error navigation tool on the Source tab")

14. Note that while SSMA can generate an HTML report, it can also generate an XML file that may be suited better to your migration environment. With the default SSMA installation, you can find the XML report in the following location: `C:\Users\demouser\Documents\SSMAProjects\[PROJECT NAME]\report\[REPORT NAME]\report.xml`.

    Here is an image of an XML report in Excel.

    ![XML report generated by SSMA opened in Excel.](./media/xml-report.png "XML report")

15. SSMA provides multiple types of information while generating reports and performing the migration. They are summarized in the table below.

    | Type       | Description     |
    | :------------- | :----------: |
    |  Warning | SSMA can convert the object, but the user should review the conversion.   |
    | Error   | SSMA cannot convert the object; user must intervene. |
    | Info   | User does not need to intervene. |

16. Next, select **Connect to Azure SQL Database** from the SSMA toolbar, to add your Azure SQL DB connection.

    ![Connect to Azure SQL Database is highlighted on the toolbar.](./media/connect-to-azure-sql-db.png "Connect to Azure SQL DB")

17. In the Connect to Azure SQL Database dialog, provide the following:

    - **Server name**: Enter the DNS name of your Azure SQL DB. It is going to be `northwind-server-[SUFFIX].database.windows.net`, where `[SUFFIX]` represents the parameter you provided to the ARM template.

    - **Database**: Northwind
    - **Authentication**: Set to **SQL Server Authentication**
      - **Username**: `demouser`
      - **Password**: Use the value you provided to the ARM template
    - **Encrypt Connection**: Check this box.
    - **Trust Server Certificate**: Check this box.

    ![The information above is entered in the Connect to Azure SQL Database dialog box, and Connect is selected at the bottom.](./media/azure-sql-db-params.png "Specify the settings")

18. Select **Connect**.

19. You will see a success message in the output window.

    ![The successful connection message is highlighted in the Output window.](./media/ssma-connect-to-azure-sql.png "View the successful connection message")

20. In the Azure SQL Database Metadata Explorer, expand the server node, then Databases. You should see Northwind listed.

    ![Northwind is highlighted under Databases in Azure SQL Database Metadata Explorer.](./media/azure-sql-db-metadata-explorer.png "Verify the Northwind listing")

21. In the Azure SQL Database Metadata explorer, check the box next to Northwind.

    ![Northwind is selected and highlighted under Databases in Azure SQL DB Metadata Explorer.](./media/northwind-db-metadata-explorer.png "Select Northwind")

22. In the SSMA toolbar, select **Convert Schema**. If the button is disabled, you can select the NW node in the Oracle Metadata Explorer, which should cause the Convert Schema button to become enabled. You can also right-click on the NW database in the Oracle Metadata Explorer.

    ![Convert Schema is highlighted on the SSMA toolbar.](./media/ssma-toolbar-convert-schema.png "Select Convert Schema")

23. After about a minute the conversion should have completed.

24. In the Azure SQL Database Metadata Explorer, observe that new schema objects have been added. For example, under Northwind, Schemas, NW, Tables you should see the tables from the Oracle database. Note that these objects have not been persisted to the Azure SQL Database instance yet.

    ![NW is selected in the Azure SQL Database Metadata Explorer, and tables from the Oracle database are visible below that.](./media/azure-sql-metadata-explorer-nw-tables.png "Observe new schema objects")

25. In the output pane, you will notice a message that the conversion finished with no errors and 17 warnings.

    ![The conversion message is highlighted in the Output window.](./media/ssma-convert-schema-output.png "View the conversion message")

    It is important to note the meaning of errors and warnings. They are not issues with the SSMA program; they indicate objects that cannot be migrated automatically by SSMA or important considerations as you complete the migration. [Here](https://docs.microsoft.com/sql/ssma/oracle/messages/o2ss0007) is an example SSMA error code in the Microsoft documentation.

26. **Optional**: Save the project. This can take a while, and is not necessary to complete the hands-on lab.

27. To apply the resultant schema to the Northwind database in Azure SQL Database, use the Azure SQL Database Metadata Explorer to view the Northwind database. Right-click Northwind, and select **Synchronize with Database**.

    ![Synchronize with Database is highlighted in the submenu of the Northwind database in Azure SQL Database Metadata Explorer.](./media/ssma-synchronize-with-database.png "Select Synchronize with Database")

28. Select **OK** in the Synchronize with the Database dialog. Wait until you see **Synchronization operation is complete** in the output window.

29. Now you need to migrate the data. In the Oracle Metadata Explorer, select **NW** and from the command bar, select **Migrate Data**.

    ![Migrate Data is highlighted in the command bar of Oracle Metadata Explorer.](./media/ssma-toolbar-migrate-data.png "Select Migrate Data")

30. You will be prompted to re-enter your Oracle credentials for use by the migration connection.

    - Recall the Oracle credentials are:

      - **Server name**: localhost
      - **Server port**: 1521
      - **Oracle SID**: XE
      - **Username**: NW
      - **Password**: oracledemo123

    - The Azure SQL Database credentials are:

      - **Server name**: Follows the format `northwind-server-[SUFFIX].database.windows.net`
      - **Authentication**: SQL Server Authentication
        - **Username**: `demouser`
        - **Password**: Use the value you provided to the ARM template.

31. Select **Connect**.

32. After the migration completes, you will be presented with a Data Migration Report, similar to the following:

    ![This is a screenshot of an example Data Migration Report.](./media/ssma-data-migration-report.png "View the Data Migration Report")

33. Select **Close** on the migration report.

### Task 2: Additional SSMA Usage Details

This lab explores a relatively simple migration. This Task will provide additional considerations that you can refer to as you attempt more complex migrations.

1. To facilitate the migration, SSMA migrates certain .NET assemblies to the target Azure SQL Database instance, as indicated by the first image. Be mindful that SQL Server 2017 and above enforce *CLR strict security*, which you must consider during your migration. As you complete other migrations, be mindful of errors such as the second image.

   ![Migrated assemblies in Azure SQL Database.](./media/assemblies-in-azure-sql-db.png "Migrated assemblies")

   ![CLR Strict Security causes .NET assembly migration to fail.](./media/ssma-synchronize-errors.png "CLR strict security")

2. To help you resolve errors during your migrations, utilize SSMA's comprehensive logs. Follow the steps below to access them.
   
   - Select **Tools** in the upper left-hand corner of SSMA. 
   - Select **Global Settings**.
   - Select the **Logging** tab on the **Global Settings** window.
   - Observe the **Log file path**.

   ![Locating the log file path in SSMA.](./media/ssma-log-file-location.png "SSMA log file path")

3. Besides the default data integrity mechanism in SSMA (comparing hashes), SSMA also provides a powerful testing suite that follows the flow outlined below.

   - Initialize test case
   - Select and configure objects
   - Select and configure affected objects
   - Call Ordering
   - Finalize test case

   Access the **Test Case Wizard**, which guides you through these steps, by selecting **Tester** and **New Test Case**.

   ![Launching the test case wizard in SSMA.](./media/test-case-wizard.png "Test case wizard")

4. Note that SSMA for Oracle also allows developers to port ad-hoc queries from Oracle to the target database engine syntax, T-SQL. To do this, in the Oracle Metadata Explorer, right-click **Statements** and select **Add Statement**.

5. In the statement editor window, paste the following code. The Oracle `ROWNUM` pseudo-column limits the number of rows in the result set. 

   ```sql
   SELECT *
   FROM NW.CATEGORIES
   WHERE ROWNUM <= 5;
   ```

   ![Oracle statement in the SSMA Statement editor.](./media/oracle-statement-ssma.png "Oracle statement with ROWNUM")

6. Right-click the new statement in the Oracle Metadata Explorer and select **Convert Schema**. Immediately, a T-SQL version of the statement should appear next to the **Azure SQL Database Metadata Explorer**.

   Notice how the `ROWNUM` Oracle pseudo-column is substituted for the T-SQL `TOP` keyword.
   
   ```sql
   SELECT TOP (5) CATEGORIES.CATEGORYID, CATEGORIES.CATEGORYNAME, CATEGORIES.DESCRIPTION, CATEGORIES.PICTURE
   FROM NW.CATEGORIES
   GO
   ```

7. Just like other objects it migrates, SSMA produces a report for the statement conversion. Simply select the **Report** tab.

   ![Statement conversion report.](./media/report-for-statement-conversion.png "Statement conversion report")
   
   >**Note:** SSMA can also run in [console mode](https://docs.microsoft.com/sql/ssma/oracle/working-with-ssma-for-oracle-console-oracletosql) to automate assessment and conversion tasks.

## Exercise 4: Migrate the Application

Duration: 15 minutes

In this exercise, you will modify the `NorthwindMVC` application so it targets Azure SQL Database instead of Oracle.

### Task 1: Create new Entity Models against Azure SQL Database and Scaffold Views

1. On your Lab VM, return to Visual Studio, and open `appsettings.json` from the Solution Explorer.

2. Add a connection string called `AzureSqlConnectionString`. Ensure that it correctly references the remote Azure SQL Database credentials.

   - Replace the value of `Server` with your Azure SQL Database DNS name.
   - Verify the value of `Password` is set.

   ```json
   "ConnectionStrings": {
      "OracleConnectionString": "DATA SOURCE=localhost:1521/XE;PASSWORD=oracledemo123;USER ID=NW",
      "AzureSqlConnectionString": "Server={Server},1433;Initial Catalog=Northwind;Persist Security Info=False;User ID=demouser;Password={Password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"
   }
   ```

3. Save the `appsettings.json` file.

    >**Note**: In production scenarios, it is not recommended to store connection strings in files that are checked into version control. Consider using Azure Key Vault references in production and [user secrets](https://docs.microsoft.com/aspnet/core/security/app-secrets) in development.

4. Open the Package Manager console by selecting **Tools** (1), **NuGet Package Manager** (2), and **Package Manager Console** (3).

    ![Opening the Package Manager console in Visual Studio.](./media/open-pmc.png "Opening the Package Manager Console")

5. Enter the following command in the Package Manager console to create the models. The `-Force` flag eliminates the need to manually clear the `Data` directory.

    ```powershell
    Scaffold-DbContext Name=ConnectionStrings:AzureSqlConnectionString Microsoft.EntityFrameworkCore.SqlServer -OutputDir Data -Context DataContext -Schemas NW -Force
    ```

    >**Note**: This command will reverse-engineer more tables than are needed. The `-Tables` flag, referencing schema-qualified table names, provides a more accurate approach.

6. Attempt to build the solution to identify errors.

    ![Errors in the solution.](./media/solution-errors.png "Solution errors")

7. Expand the **Views** folder. Delete the following folders, each of which contains five views:

   - **Customers**
   - **Employees**
   - **Products**
   - **Shippers**
   - **Suppliers**

8. Expand the **Controllers** folder. Delete all controllers, except **HomeController.cs**.

9. Open **DataContext.cs**. Add the following line to the top of the file, below the other `using` directives.

    ```csharp
    using NorthwindMVC.Models;
    ```

    Add the following below the other property definitions.

    ```csharp
    // Add SalesByYearDbSet
    public virtual DbSet<SalesByYear> SalesByYearDbSet { get; set; }
    ```

    Lastly, add the following statement to the `OnModelCreating()` method, after setting the collation information. 

    ```csharp
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        // Collation information

        // Add this:
        modelBuilder.Entity<SalesByYear>(entity =>
        {
            entity.HasNoKey();
        });

        // Other Fluent API configuration
    }
    ```

10. Build the solution. Ensure that no errors appear. We added `SalesByYearDbSet` to **DataContext** because **HomeController.cs** references it. We deleted the controllers and their associated views because we will scaffold them again from the models.

    > **Note**: If you encounter build errors, verify that the `_ViewImports.cshtml` file in the `Views` directory was not accidentally deleted. If it was, simply copy the [file from the project GitHub repository](https://raw.githubusercontent.com/microsoft/MCW-Migrating-Oracle-to-Azure-SQL-and-PostgreSQL/master/Hands-on%20lab/lab-files/starter-project/NorthwindMVC/Views/_ViewImports.cshtml).

11. Right-click the **Controllers** folder and select **Add** (1). Select **New Scaffolded Item...** (2).

   ![Adding a new scaffolded item.](./media/add-scaffolded-item.png "New scaffolded item")

12. Select **MVC Controller with views, using Entity Framework**. Then, select **Add**.

   ![Add MVC Controller with Views, using Entity Framework.](./media/add-mvc-with-ef.png "MVC Controller with Views, using Entity Framework")

13. In the **ADD MVC Controller with views, using Entity Framework** dialog box, provide the following details. Then, select **Add**. Visual Studio will build the project.

    - **Model class**: Select `Customer`.
    - **Data context class**: Select `DataContext`.
    - Select all three checkboxes below **Views**.
    - **Controller name**: Keep it set to `CustomersController`.

   ![Scaffolding controllers and views from model classes.](./media/customer-scaffold-views.png "Scaffolding controllers and views")

14. Repeat steps 11-13, according to the following details:

    - **EmployeesController.cs**
      - Based on the **Employee** model class
    - **ProductsController.cs**
      - Based on the **Product** model class
    - **ShippersController.cs**
      - Based on the **Shipper** model class
    - **SuppliersController.cs**
      - Based on the **Supplier** model class

15. Navigate to **Startup.cs**. Ensure that SQL Server is configured as the correct provider and the appropriate connection string is referenced.

    ```csharp
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllersWithViews();

        // Verify this line:
        services.AddDbContext<DataContext>(options => options.UseSqlServer(Configuration.GetConnectionString("AzureSqlConnectionString")));
    }
    ```

### Task 2: Ensure Application Compatibility with the Stored Procedure

1. Open the file `HomeController.cs`, in the Controllers folder in the Solution Explorer.

   ![The HomeController.cs file is selected and highlighted under the Controllers folder in Solution Explorer.](./media/visual-studio-solution-explorer-controllers-home-controller.png "Open HomeController.cs")

2. Comment out the code under the Oracle comment. First, select the lines for the Oracle code, then select the Comment button in the toolbar.

   ![The code under the Oracle comment is highlighted and labeled 1, and the Comment button in the toolbar is highlighted and labeled 2.](./media/visual-studio-home-controller-comment-out-oracle-lines.png "Comment out code")

3. Next, add the following code below the commented Oracle stored procedure call. Notice how it excludes the cursor output parameter.

   ```csharp
   var salesByYear = await _context.SalesByYearDbSet.FromSqlRaw(
      "exec [NW].[SALESBYYEAR] @p_begin_date, @p_end_date ",
      new SqlParameter("p_begin_date", "1996-1-1"),
      new SqlParameter("p_end_date", "1999-1-1")).ToListAsync();
   ```

4. Save the changes to `HomeController.cs`.

5. Open the file, `SALESBYYEAR.cs`, in the Models folder in the Solution Explorer.

    ![SALESBYYEAR.cs is highlighted under the Models folder in the Solution Explorer.](./media/visual-studio-models-salesbyyear.png "Open SALESBYYEAR.cs")

6. Change the `YEAR` property from string to int.

    ![The int property is highlighted.](./media/visual-studio-models-salesbyyear-updated.png "Change YEAR property")

7. Save the file.

8. Open the `SalesByYearViewModel.cs` file from the Models folder in the Solution Explorer.

    ![SalesByYearViewModel.cs is highlighted under the Models folder in the Solution Explorer.](./media/visual-studio-models-salesbyyearviewmodel.png "Open SalesByYearViewModel.cs")

9. Change the type of the `YEAR` property from string to int, then save the file.

    ![The int property value is highlighted.](./media/visual-studio-models-salesbyyearviewmodel-updated.png "Change the YEAR property")

10. Run the solution by selecting the green Start button on the toolbar.

    ![Start is highlighted on the toolbar.](./media/visual-studio-toolbar-start.png "Select Start")

11. You will get an exception that the stored procedure call has failed. This is because of an error in migrating the stored procedure.

    ![An exception appears indicating that the stored procedure call has failed.](./media/visual-studio-exception-sqlexception.png "View the error")

12. Select the red Stop button to end execution of the application.

    ![The Stop button is highlighted on the toolbar.](./media/visual-studio-toolbar-stop.png "Select Stop")

13. To resolve the error, open the `SALES_BY_YEAR_fix.sql` file, located under Solution Items in the Solution Explorer.

14. From the Visual Studio menu, select **View**, and then **Server Explorer**.

    ![View and Server Explorer are highlighted in the Visual Studio menu.](./media/visual-studio-menu-view-server-explorer.png "Select Server Explorer")

15. In the Server Explorer, right-click on **Data Connections**, and select **Add Connections...**

    ![Data Connections is selected in Server Explorer, and Add Connection is highlighted in the shortcut menu.](./media/visual-studio-server-explorer-data-connections.png "Select Add Connection")

16. On the Choose Data Source dialog, select **Microsoft SQL Server**, and select **Continue**.

    ![Microsoft SQL Server is selected and highlighted under Data source in the Choose Data Source dialog box.](./media/visual-studio-server-explorer-data-connections-add.png "Select Microsoft SQL Server")

17. On the Add Connection dialog, enter the following:

    - **Data source**: Leave Microsoft SQL Server (SqlClient).
    - **Server name**: Enter the DNS name of the Azure SQL DB instance.
    - **Authentication**: Select SQL Server Authentication.
    - **Username**: demouser.
    - **Password**: Provide the password you configured for `demouser`.
    - **Connect to a database**: Choose Select or enter a database name, and enter Northwind.
    - Select **Test Connection** to verify your settings are correct, and select **OK** to close the successful connection dialog.

    ![The information above is entered in the Add Connection dialog box, and Test Connection is selected at the bottom.](./media/visual-studio-server-explorer-data-connections-add-connection.png "Specify the settings")

18. Select **OK**.

19. Right-click the newly added Azure SQL DB connection in the Server Explorer, and select **New Query**.

    ![The newly added SQL Server connection is selected in Server Explorer, and New Query is highlighted in the shortcut menu.](./media/visual-studio-server-explorer-data-connections-new-query.png "Select New Query")

20. Select and copy all of the text from the `SALES_BY_YEAR_fix.sql` file (click CTRL+A, CTRL+C in the `SALES_BY_YEAR_fix.sql` file).

21. Paste (CTRL+V) the copied text into the new Query window.

22. Verify `Use [Northwind]` is the first line of the file, and that it matches the database listed in the query bar, then select the green **Execute** button.

    ![The Use [Northwind] statement is highlighted, as is the Northwind database and the Execute button in the query bar.](./media/visual-studio-sql-query-execute.png "Verify the Use [Northwind] statement")

23. You should see a message that the command completed successfully.

    ![This is a screenshot of a message that the command completed successfully.](./media/visual-studio-sql-query-completed-successfully.png "View the message")

24. Run the application again by selecting the green Start button in the Visual Studio toolbar.

    ![The Start button is highlighted on the Visual Studio toolbar.](./media/visual-studio-toolbar-start.png "Select Start")

25. Verify the graph is showing correctly on the Northwind Traders dashboard.

    ![The Northwind Traders Dashboard is visible in a browser.](./media/northwind-traders-dashboard.png "View the dashboard")

26. Congratulations! You have successfully migrated the data and application from Oracle to SQL Server.

## Exercise 5: Configure SQL Server instances (Optional Homogenous Migration)

Duration: 45 minutes

In this exercise, you will configure SQL Server 2008 R2 on the SqlServer2008 VM. The database on this VM will act like the customer's existing on-premises database for this hands-on lab.

### Task 1: Connect to the SqlServer2008 VM

In this task, you will create an RDP connection to the SqlServer2008 VM.

1. In the [Azure portal](https://portal.azure.com), select **Resource groups** in the Azure services list, enter your resource group name (hands-on-lab-SUFFIX) into the filter box, and select it from the list.

   ![Resource groups is selected in the Azure navigation pane, hands is entered into the filter box, and the hands-on-lab-SUFFIX resource group is highlighted.](./media/resource-groups.png "Resource groups list")

2. In the list of resources for your resource group, select the SqlServer2008 VM.

   ![The list of resources in the hands-on-lab-SUFFIX resource group is displayed, and SqlServer2008 is highlighted.](media/resource-group-resources-sqlserver2008r2.png "SqlServer2008 VM in resource group list")

3. On the SqlServer2008 blade, select Connect from the top menu.

   ![The SqlServer2008 blade is displayed, with the Connect button highlighted in the top menu.](media/connect-vm.png "Connect to SqlServer2008")

4. Select **Download RDP file**, then open the downloaded RDP file.

   ![The Connect to virtual machine blade is displayed, and the Download RDP file button is highlighted.](./media/connect-to-virtual-machine.png "Connect to virtual machine")

5. Select **Connect** on the Remote Desktop Connection dialog.

   ![In the Remote Desktop Connection Dialog Box, the Connect button is highlighted.](./media/remote-desktop-connection.png "Remote Desktop Connection dialog")

6. Enter the following credentials when prompted:

   - **Username**: demouser
   - **Password**: Password set in the ARM template

7. Select **Yes** to connect, if prompted that the identity of the remote computer cannot be verified.

   ![In the Remote Desktop Connection dialog box, a warning states that the identity of the remote computer cannot be verified, and asks if you want to continue anyway. At the bottom, the Yes button is circled.](./media/remote-desktop-connection-identity-verification-sqlserver2008r2.png "Remote Desktop Connection dialog")

### Task 2: Install AdventureWorks sample database

In this task, you will install the AdventureWorks database in SQL Server 2008 R2. It will act as the on-premises data warehouse database that you will migrate to Azure SQL Database.

1. On the SqlServer2008 VM, open a web browser, and navigate to the GitHub site containing the sample AdventureWorks 2008 R2 database at <https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks2008r2>.

2. Scroll down under Assets, and select `adventure-works-2008r2-dw.script.zip`.

   ![The adventure-works-2008r2-dw-script.zip download link is highlighted under Assets for the sample database.](./media/adventure-works-2008-r2-sample-download.png "Assets list")

3. Save the file, and unzip the downloaded file to a folder you create, called `C:\AdventureWorksSample`.

4. Open **Microsoft SQL Server Management Studio 17** (SSMS) on the SqlServer2008 VM. It can be found under Start->All Programs->Microsoft SQL Server Tools 17.

5. Connect to the **SQLSERVER2008** database if you are not already connected. In the Connect to Server dialog, leave Authentication set to **Windows Authentication**, and select **Connect**.

   ![Connect to Server dialog, with SQLSERVER2008 specified as the Server name and Authentication set to Windows Authentication.](media/sql-server-connection-sqlserver2008.png "Connect to Server")

6. In SSMS, select the **Open File** icon in the menu bar.

   ![The File icon is highlighted on the SSMS menu bar.](./media/ssms-toolbar-open-file.png "Select Open File")

7. In the Open File dialog, browse to the `C:\AdventureWorksSample\AdventureWorks 2008R2 Data Warehouse\` folder, select the file named `instawdwdb.sql`, and then select **Open**.

   ![Local Disk (C:) is selected on the left side of the Open File dialog box, and instawdwdb.sql is selected and highlighted on the right.](./media/ssms-open-file-dialog.png "Select instawdwdb.sql")

8. Next, select **Tools** in the SSMS menu, then select **Options**.

   ![Tools are highlighted on the SSMS menu bar, and Options is highlighted at the bottom.](./media/ssms-tools-options-dialog.png "Select Options")

9. In the Options dialog, expand **Text Editor** in the tree view on the left, then expand **Transact-SQL**, select **General**, then check the box next to **Line numbers**. This will display line numbers in the query editor window, to make finding the lines specified below easier.

   ![On the left side of the Options dialog box, Text Editor is highlighted, Transact-SQL is highlighted below that, and General is selected and highlighted below that. On the right, Line numbers are selected and highlighted.](./media/ssms-tools-options-text-editor-tsql-general.png "Display line numbers in the query editor")

10. Select **OK** to close the Options dialog.

11. In the SSMS query editor for `instawdwdb.sql`, uncomment the `SETVAR` lines (lines 36 and 37) by removing the double hyphen "--" from the beginning of each line.

12. Next, edit the file path for each variable so they point to the following (remember to include a trailing backslash ("\\") on each path):

    - SqlSamplesDatabasePath: `C:\AdventureWorksSample\`

    - SqlSamplesSourceDataPath: `C:\AdventureWorksSample\AdventureWorks 2008R2 Data Warehouse\`

      ![The variables and file paths specified above are highlighted in the SSMS query editor.](./media/ssms-query-editor-instawdwdb.png "Edit the variable file paths")

13. Place SSMS into **SQLCMD mode** by selecting it from the **Query** menu.

    ![SQLCMD Mode is highlighted in the Query menu.](./media/ssms-query-menu-sqlcmd-mode.png "Select SQLCMD Mode")

14. Execute the script by selecting the **Execute** button on the toolbar in SSMS.

    ![Execute is highlighted on the SSMS toolbar.](./media/ssms-toolbar-execute.png "Run the script")

15. This will create the `AdventureWorksDW2008R2` database. When the script is done running, you will see output similar to the following in the results pane.

    ![Output is displayed in the results pane. At this time, we are unable to capture all of the information in the window. Future versions of this course should address this.](./media/ssms-query-instawdwdb-script-output.png "View the script output")

16. Expand **Databases** in Object Explorer, right-click the `AdventureWorksDW2008R2` database, and select **Rename**.

    ![On the left side of Object Explorer, Databases is highlighted, AdventureWorksDW2008R2 is highlighted below that, and Rename is selected and highlighted in the submenu.](./media/ssms-databases-rename.png "Rename in Object Explorer")

17. Set the name of the database to `WideWorldImporters`.

    ![WideWorldImporters is highlighted under Databases in Object Explorer.](./media/ssms-databases-wideworldimporters.png "Name the database")

18. Close SSMS.

### Task 3: Update SQL Server settings using Configuration Manager

In this task, you will update the SQL Server service accounts and other settings associated with the SQL Server instance installed on the VM.

1. From the Start Menu on your SqlServer2008 VM, search for **SQL Server Config**, then select **SQL Server Configuration Manager** from the search results.

   ![SQL Server Configuration Manager is selected and highlighted in the search results.](media/windows-server-2008-search-sql-server-config.png "SQL Server Configuration Manager")

2. From the tree on the left of the Configuration Manager window, select **SQL Server Services**, and then double-click **SQL Server (MSSQLSERVER)** in the list of services to open its properties dialog.

   ![SQL Server Services is highlighted on the left side of SQL Server Configuration Manager.](media/sql-server-configuration-manager-sql-server-services.png "Select SQL Server Services")

3. In the SQL Server (MSSQLSERVER) Properties dialog, change **Log on as** to use the demouser account, by entering **demouser** into the Account Name box, then entering the password set in the ARM template into the Password and Confirm password boxes.

   ![The above credentials are highlighted in the SQL Server (MSSQLSERVER) Properties dialog box.](media/sql-server-2008-configuration-manager-sql-server-mssqlserver-properties.png "Enter demouser credentials")

4. Select **OK**.

5. Select **Yes** to restart the service in the **Confirm Account Change** dialog.

6. While still in the SQL Server Configuration Manager, expand **SQL Server Network Configuration**, select **Protocols for MSSQLSERVER**, and double-click **TCP/IP** to open the properties dialog.

   ![Protocols for MSSQLSERVER are highlighted on the left side of SQL Server Configuration Manager, and TCP/IP is highlighted in the Protocol Name list on the right.](media/sql-server-configuration-manager-protocols-for-mssqlserver.png "Select TCP/IP")

7. On the TCP/IP Properties dialog, ensure **Enabled** is set to **Yes**, and select **OK**.

   ![Enabled is selected on the Protocol tab of the TCP/IP Properties dialog box.](media/sql-server-configuration-manager-protocols-tcp-ip-properties.png "Enable TCP/IP")

   > **Note**: If prompted that the changes will not take effect until the service is restarted, select **OK**. You will restart the service later.

8. Select **SQL Server Services** in the tree on the left, then right-click **SQL Server (MSSQLSERVER)** in the services pane, and select **Restart**.

   ![SQL Server Services is highlighted on the left side of SQL Server Configuration Manager, SQL Server (MSSQLSERVER) is highlighted on the right, and Restart is highlighted in the submenu.](media/sql-server-configuration-manager-sql-server-services-sql-server-restart.png "Select Restart")

9. Repeat the previous step for the **SQL Server Agent (MSSQLSERVER)** service, this time selecting **Start** from the menu.

   ![SQL Server Services is highlighted on the left side of SQL Server Configuration Manager, SQL Server Agent (MSSQLSERVER) is highlighted on the right, and Start is highlighted in the submenu.](media/sql-server-configuration-manager-sql-server-services-sql-server-agent-start.png "Select Start")

10. Close the SQL Server Configuration Manager.

## Exercise 6: Migrate SQL Server to Azure SQL Database using DMS (Optional Homogenous Migration)

Duration: 60 minutes

Wide World Importers would like a Proof of Concept (POC) that moves their data warehouse to Azure SQL Database. They would like to know about any incompatible features that might block their eventual production move. In this exercise, you will use the [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) (DMS) to assess their SQL Server 2008 R2 data warehouse database, and then migrate the WideWorldImporters database from the on-premises SQL Server 2008 R2 instance to [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/).

### Task 1: Assess the on-premises database

Wide World Importers would like an assessment to see what potential issues they would have to address in moving their database to Azure SQL Database.

1. In the SQL Server 2008 instance, launch the Data Migration Assistant.

2. In the Data Migration Assistant window, select the New **(+)** icon in the left-hand menu.

   ![+ New is selected and highlighted in the Data Migration Assistant window.](./media/data-migration-assistant-new-project.png "Select + New")

3. In the New project dialog, enter the following:

   - **Project type**: Select Assessment.
   - **Project name**: Enter Assessment.
   - **Assessment type**: Select Database Engine.
   - **Source server type**: Select SQL Server.
   - **Target server type**: Select Azure SQL Database.

   ![The above information is entered in the New project dialog box.](./media/data-migration-assistant-new-project-assessment.png "Enter information in the New project dialog box")

   - Select **Create**.

4. On the **Options** tab, ensure the **Check database compatibility** and **Check feature parity** report types are checked, and select **Next**.

   ![Check database compatibility and Check feature parity are selected and highlighted on the Options screen.](./media/data-migration-assistant-options.png "Select the report types")

5. In the **Connect to a server** dialog on the **Select sources** tab, enter `SQLSERVER2008` into the Server name box, and **uncheck Encrypt connection**, then select **Connect**.

   ![In the Connect to a server dialog box, SQLSERVER2008 is highlighted in the Server name box, and Encrypt connection is unchecked and highlighted below that in the Connect to a server dialog box.](./media/data-migration-assistant-select-sources-sqlserver2008.png "Enter information in the Connect to a server dialog box")

6. In the **Add sources** dialog that appears, check the box next to **WideWorldImporters**, and select **Add**.

   ![WideWorldImporters is selected and highlighted under SQLSERVER2008 in the Add sources dialog box.](./media/data-migration-assistant-select-sources-sqlserver2008-wideworldimporters.png "Select WideWorldImporters")

7. Select **Start Assessment**.

8. Review the Assessment results, selecting both **SQL Server feature parity** and **Compatibility issues** options and viewing the reports.

    ![Various information is selected on the Review results screen.](./media/data-migration-assistant-review-results-sqlserver2008-wideworldimporters.png "Review the Assessment results")

9. You now have a list of the issues WWI will need to consider in upgrading their database to Azure SQL Database. Notice the assessment includes recommendations on the potential resolutions to issues. You can select **Export Assessment** on the top toolbar to save the report as a JSON file, if desired.

### Task 2: Migrate the database schema

After you have reviewed the assessment results and you have ensured the database is a candidate for migration to Azure SQL Database, use the Data Migration Assistant to migrate the schema to Azure SQL Database.

1. On the SqlServer2008 VM, return to the Data Migration Assistant, and select the New **(+)** icon in the left-hand menu.

2. In the New project dialog, enter the following:

   - **Project type**: Select Migration.
   - **Project name**: Enter DwMigration.
   - **Source server type**: Select SQL Server.
   - **Target server type**: Select Azure SQL Database.
   - **Migration scope**: Select Schema only.

   ![The above information is entered in the New project dialog box.](media/data-migration-assistant-new-project-migration.png "New Project dialog")

3. Select **Create**.

4. On the **Select source** tab, enter the following:

   - **Server name**: Enter SQLSERVER2008.
   - **Authentication type**: Leave Windows Authentication selected.
   - **Connection properties**: Check both Encrypt connection and Trust server certificate.
   - Select **Connect**.
   - Select **WideWorldImporters** from the list of databases.

   ![The Select source tab of the Data Migration Assistant is displayed, with the values specified above entered into the appropriate fields.](media/data-migration-assistant-migration-select-source.png "Data Migration Assistant Select source")

5. Select **Next**.

6. On the **Select target** tab, enter the following:

   - **Server name**: Enter the server name of the Azure SQL Database you created. If you deployed the ARM template, it will follow the format `northwind-server-[SUFFIX].database.windows.net`.
     - To find the name of your SQL Database, select the WideWorldImportersDW SQL Database from your hands-on-lab-SUFFIX resource group in the Azure portal, and then select the **Server name** in the Essentials area of the Overview blade.

   ![On the SQL database Overview blade, the Server name is highlighted.](media/azure-sql-database-server-name.png "SQL Database Overview")

   - **Authentication type**: Select SQL Server Authentication.
   - **Username**: demouser
   - **Password**: Password set in the ARM template
   - **Connection properties**: Check both Encrypt connection and Trust server certificate.
   - Select **Connect**.
   - Select **WideWorldImportersDW** from the list of databases.

   ![The Select target tab of the Data Migration Assistant is displayed, with the values specified above entered into the appropriate fields.](media/data-migration-assistant-migration-select-target.png "Data Migration Assistant Select target")

7. Select **Next**.

8. In the **Select objects** tab, leave all the objects checked, and select **Generate SQL script**.

   ![The Select objects tab of the Data Migration Assistant is displayed, with all the objects checked.](media/data-migration-assistant-migration-select-objects.png "Data Migration Assistant Select target")

9. In the **Script & deploy schema** tab, review the script, then select **Deploy schema**.

   ![The Script & deploy schema tab of the Data Migration Assistant is displayed, with the generated script shown.](media/data-migration-assistant-migration-script-and-deploy-schema.png "Data Migration Assistant Script & deploy schema")

10. Select **Deploy schema**.

11. After the schema is deployed, review the deployment results, and ensure there were no errors.

    ![The schema deployment results are displayed, with 226 commands executed and 0 errors highlighted.](media/data-migration-assistant-migration-deployment-results.png "Schema deployment results")

12. Next, open SSMS on the SqlServer2008 VM, and connect to your Azure SQL Database, by selecting **Connect->Database Engine** in the Object Explorer, and then entering the server name and credentials into the Connect to Server dialog.

    ![The SSMS Connect to Server dialog is displayed, with the Azure SQL Database name specified, SQL Server Authentication selected, and the demouser credentials entered.](media/ssms-connect-azure-sql-database.png "Connect to Server")

13. Once connected, expand **Databases**, and expand **WideWorldImportersDW**, then expand **Tables**, and observe the schema has been created.

    ![In the SSMS Object Explorer, Databases, WideWorldImportersDW, and Tables are expanded, showing the tables created by the deploy schema script.](media/ssms-databases-wideworldimporters-tables.png "SSMS Object Explorer")

### Task 3: Create a migration project

In this task, you create a new migration project for the WideWorldImporters database.

1. Navigate to the Azure Database Migration Service in the [Azure portal](https://portal.azure.com). It is easiest to locate your instance of Database Migration Service from the hands-on-lab-SUFFIX resource group.

   ![Locate the wwi-dms instance of Azure Database Migration Service from the hands-on-lab-SUFFIX resource group.](./media/locate-wwi-dms-in-rg.png "Locating wwi-dms using search filters in the hands-on-lab-SUFFIX resource group")

2. On the Azure Database Migration Service blade, select **+New Migration Project**.

   ![On the Azure Database Migration Service blade, +New Migration Project is highlighted in the toolbar.](media/dms-add-new-migration-project.png "Azure Database Migration Service New Project")

3. On the New migration project blade, enter the following:

   - **Project name**: Enter OnPremToAzureSql.

   - **Source server type**: Select SQL Server.

   - **Target server type**: Select Azure SQL Database.

   - **Choose type of activity**: Select Create project only.

     ![The New migration project blade is displayed, with the values specified above entered into the appropriate fields.](media/dms-new-migration-project-blade.png "New migration project")

4. Select **Create**.

5. On the Migration Wizard **Select source** blade, enter the following:

   - **Source SQL Server instance name**: Enter the IP address of your SqlServer2008 VM. For example, `40.84.6.199`.

     - You can retrieve the VM IP address by navigating to the SqlServer2008 overview blade in the Azure portal and selecting the copy button next to the Public IP address value.

       ![On the SqlServer2008 VM Overview blade, the Public IP address is highlighted.](media/sql-virtual-machine-overview-blade-ip-address.png "Virtual machine Overview")

   - **Authentication type**: Select SQL Authentication.
   - **Username**: demouser
   - **Password**: Password set in the ARM template
   - **Connection properties**: Check both Encrypt connection and Trust server certificate.

   ![The Migration Wizard Select source blade is displayed, with the values specified above entered into the appropriate fields.](media/dms-source-sql-server.png "Migration Wizard Select source")

6. Select **Next: Select databases >>**.

7. On the Migration Wizard **Select databases** blade, select WideWorldImporters.

   ![The Migration Wizard Select databases blade is displayed, with the WideWorldImporters database selected.](media/dms-select-source-db.png "Migration Wizard Select databases")

8. Select **Next: Select target >>**.

9.  On the Migration Wizard **Select target** blade, enter the following:

   - Select **I know my target details**.
   - **Target server name**: Enter the server name for your Azure SQL Database.

     - To find the name of your SQL Database, select the WideWorldImportersDW SQL Database from your hands-on-lab-SUFFIX resource group in the Azure portal, and then select the **Server name** in the Essentials area of the Overview blade.

       ![On the SQL database Overview blade, the Server name is highlighted.](media/azure-sql-database-server-name.png "SQL Database Overview")

   - **Authentication type**: Select SQL Authentication.
   - **Username**: demouser
   - **Password**: Password set in the ARM template
   - **Connection properties**: Check Encrypt connection.

   ![The Migration Wizard Select target blade is displayed, with the values specified above entered into the appropriate fields.](media/dms-select-target-db.png "Migration Wizard Select target")

11. Select **Next: Summary >>**.

12. On the Migration Wizard Summary blade, review the Project summary, then select **Save project**.

    ![The Migration Wizard summary blade is displayed.](media/dms-migration-project-summary.png "Migration Wizard summary")

### Task 4: Run the migration

In this task, you will create a new activity in the Azure Database Migration Service to execute the migration from the on-premises SQL Server 2008 R2 server to Azure SQL Database.

1. On the Azure Database Migration Service blade, select **+New Activity**, and then select **Data migration**.

   ![On the Azure Database Migration Service blade, +New Activity is highlighted, and the Data migration button is highlighted in the Create new activity dialog.](media/dms-add-new-activity.png "Azure Database Migration Service Add New Activity")

2. On the Migration Wizard **Select source** blade, re-enter the demouser password and then select **Next: Select databases >>**.

   ![The Migration Wizard Select source blade is displayed, with the password value highlighted.](media/dms-migration-activity-source.png "Migration Wizard Select source")

3. On the Migration Wizard **Select databases** blade, select **WideWorldImporters**. Then, select **Next: Select target >>**.

   ![Select the WideWorldImporters source database.](./media/source-database.png "WideWorldImporters source database")

4. On the Migration Wizard **Select target** blade, re-enter the demouser password and then select **Next: Map to target databases >>**.

   ![The Migration Wizard Select target blade is displayed, with the password value highlighted.](media/dms-migration-activity-target.png "Migration Wizard Select target")

5. On the Migration Wizard **Map to target databases** blade, confirm that **WideWorldImporters** is checked as the source database, and that **WideWorldImportersDW** is selected as the target. Then select **Next: Configure migration settings >>**.

   ![The Migration Wizard Map to target database blade is displayed, with the WideWorldImporters line highlighted.](media/dms-migration-activity-select-target-db.png "Migration Wizard Map to target databases")

6. On the Migration Wizard **Configure migration settings** blade, expand the WideWorldImporters database, verify all the tables are selected, and select **Next: Summary >>**.

   ![The Migration Wizard Configure migration settings blade is displayed, with the expand arrow for WideWorldImporters highlighted, and all the tables checked.](media/dms-migration-activity-confirm-source-tables.png "Migration Wizard Configure migration settings")

7. On the Migration Wizard **Summary** blade, enter the following:

   - **Activity name**: Enter a name, such as DwMigration.

   ![The Migration Wizard summary blade is displayed, DwMigration is entered into the name field, and Validate my database(s) is selected in the Choose validation option blade, with all three validation options selected.](media/dms-migration-activity-start.png "Migration Wizard Summary")

8. Select **Start migration**.

9.  Monitor the migration on the status screen that appears. Select the refresh icon in the toolbar to retrieve the latest status.

   ![On the Migration job blade, the status shows the job is Running.](media/dms-migration-wizard-status-running.png "Migration with Pending status")

11. When the migration is complete, you will see the status as **Completed**.

   ![On the Migration job blade, the status of Completed is highlighted](media/dms-migration-wizard-status-complete.png "Migration with Completed status")

### Task 5: Verify data migration

In this task, you will use SSMS to verify the database was successfully migrated to Azure SQL Database.

1. Open SSMS on the SqlServer2008 VM, and connect to your Azure SQL Database. In the Connect to Server dialog, enter the following:

   - **Server name**: Enter the server name of your Azure SQL Database.
   - **Authentication**: Select SQL Server Authentication.
   - **Login**: demouser
   - **Password**: Password set in the ARM template

   ![The SSMS Connect to Server dialog is displayed, with the Azure SQL Database name specified, SQL Server Authentication selected, and the demouser credentials entered.](media/ssms-connect-azure-sql-database.png "Connect to Server")

2. Select **Connect**.

3. In the Object Explorer, expand Databases, WideWorldImportersDW, and Tables, then right-click `dbo.DimCustomer`, and choose **Select Top 1000 Rows**.

   ![In SSMS, Databases, WideWorldImportersDW, and Tables are expanded, and the context menu for dbo.DimCustomer is displayed, with Select Top 1000 Rows highlighted in the menu.](media/ssms-select-top.png "Select Top 100 Rows")

4. Observe that the query returns results, showing the data has been migrated from the on-premises SQL Server 2008 R2 database into Azure SQL Database.

5. Leave SSMS open with the connection to your Azure SQL Database for the next exercise.

## Exercise 7: Post upgrade enhancement (Optional Homogenous Migration)

Duration: 20 minutes

In this exercise, you will demonstrate value from the upgrade by enabling the Table Compression and ColumnStore Index features of Azure SQL Database.

### Task 1: Table compression

1. In SSMS on the SqlServer2008 VM, and connect to your Azure SQL Database.

2. Open a new query window by selecting **New Query** from the toolbar.

   ![The New Query icon is highlighted on the SSMS toolbar.](./media/ssms-toolbar-new-query.png "SSMS New Query")

3. Copy the script below, and paste it into the query window:

   ```sql
   USE [WideWorldImportersDW]

   -- Get the Size of the FactInternetSales table
   SELECT
   t.Name AS TableName,
   p.rows AS RowCounts,
   CAST(ROUND((SUM(a.total_pages) / 128.00), 2) AS NUMERIC(36, 2)) AS Size_MB
   FROM sys.tables t
   INNER JOIN sys.indexes i ON t.OBJECT_ID = i.object_id
   INNER JOIN sys.partitions p ON i.object_id = p.OBJECT_ID AND i.index_id = p.index_id
   INNER JOIN sys.allocation_units a ON p.partition_id = a.container_id
   WHERE t.Name = 'FactInternetSales'
   GROUP BY t.Name, p.Rows
   GO
   ```

4. Select **Execute** on the toolbar to run the query to retrieve the size of the `FactInternetSales` table.

   ![The Execute icon is highlighted on the SSMS toolbar.](./media/ssms-toolbar-execute-query.png "Select Execute")

5. In the results pane, note the size of the `FactInternetSales` table.

   ![In the SSMS results pane, the size of the uncompressed FactInternetSales table is highlighted.](media/ssms-query-results-factinternetsales-uncompressed-size.png "Query results")

6. In the Object Explorer, expand Databases, WideWorldImportersDW, and Tables.

7. Right-click the `FactInternetSales` table, select the **Storage** context menu, and then select **Manage Compression** from the fly-out menu.

   ![The FactInternetSales table is selected on the left, Storage is selected and highlighted in the submenu, and Manage Compression is highlighted on the right.](./media/ssms-table-storage-context-menu.png "Select Manage Compression")

8. On the Welcome page of the Data Compression Wizard, select **Next**.

9. On the Select Compression Type page, select **Row** from the Compression Type drop-down, and select **Next**.

   ![The Row compression type is highlighted on the Select Compression Type screen of the Data Compression Wizard.](media/ssms-data-compression-wizard-compression-type-row.png "Data Compression Wizard Select Compression Type")

10. On the Select an Output Option page, select **Run immediately**, and then select **Finish >>|**.

    ![Run immediately is highlighted on the Select an Output Option screen of the Data Compression Wizard.](media/ssms-data-compression-wizard-output-row-compression.png "Data Compression Wizard Select an Output Option")

11. Select **Finish** on the Summary page and **Close** when the compression wizard is finished.

    ![The Summary screen of the Data Compression Wizard is displayed.](media/ssms-data-compression-wizard-summary-row-compression.png "Data Compression Wizard Summary")

12. Rerun the query to get the size of the `FactInternetSales` table, noting the reduced size of the table.

    ![In the SSMS results pane, the size of the row compressed FactInternetSales table is highlighted.](media/ssms-query-results-factinternetsales-row-compression-size.png "Query results")

13. Now, repeat steps 7 - 12 above, this time setting the Compression type to Page.

    ![The Page compression type is highlighted on the Select Compression Type screen of the Data Compression Wizard.](media/ssms-data-compression-wizard-compression-type-page.png "Data Compression Wizard Select Compression Type")

14. Once again, observe the table size in the results pane, and compare it to the values noted for the uncompressed table and with Row compression applied.

    ![In the SSMS results pane, the size of the page compressed FactInternetSales table is highlighted.](media/ssms-query-results-factinternetsales-page-compression-size.png "Query results")

15. Both Row and Page compression reduce the size of the table, but Page compression provides the greatest reduction in this case. Compression decreases the load on the Disk I/O subsystem, while increasing the load on the CPU. Since most data warehouse workloads are heavily disk-bound, and often have low CPU usage, compression can be a great way to improve performance.

### Task 2: Clustered ColumnStore index

In this task, you will create a new table based on the existing `FactResellerSales` table and apply a ColumnStore index.

1. In SSMS, ensure you are connected to the Azure SQL Database instance.

2. Open a new query window by selecting **New Query** from the toolbar.

   ![The New Query icon is highlighted on the SSMS toolbar.](./media/ssms-toolbar-new-query.png "SSMS New Query")

3. Copy the script below, and paste it into the query window:

   ```sql
   USE WideWorldImportersDW

   SELECT *
   INTO ColumnStore_FactResellerSales
   FROM FactResellerSales
   GO
   ```

4. Select **Execute** on the toolbar to run the query, and create a new table named `ColumnStore_FactResellerSales`, populated with data from the `FactResellerSales` table.

   ![The Execute icon is highlighted on the SSMS toolbar.](./media/ssms-toolbar-execute-query.png "Select Execute")

5. Select **New Query** in the toolbar again, and paste the following query into the new query window. The query contains multiple parts; one to get the size of the `ColumnStore_FactResellerSales` table, the second to create a clustered ColumnStore index on the ColumnStore_FactResellerSales table, and then the size query is repeated to get the size after adding the clustered ColumnStore index.

   ```sql
   USE [WideWorldImportersDW]

   -- Get the Size of the ColumnStore_FactResellerSales table
   SELECT
   t.Name AS TableName,
   p.rows AS RowCounts,
   CAST(ROUND((SUM(a.total_pages) / 128.00), 2) AS NUMERIC(36, 2)) AS Size_MB
   FROM sys.tables t
   INNER JOIN sys.indexes i ON t.OBJECT_ID = i.object_id
   INNER JOIN sys.partitions p ON i.object_id = p.OBJECT_ID AND i.index_id = p.index_id
   INNER JOIN sys.allocation_units a ON p.partition_id = a.container_id
   WHERE t.Name = 'ColumnStore_FactResellerSales'
   GROUP BY t.Name, p.Rows
   GO

   -- Create a clustered columnstore index on the ColumnStore_FactResellerSales table
   CREATE CLUSTERED COLUMNSTORE INDEX [cci_FactResellerSales]
   ON [dbo].[ColumnStore_FactResellerSales]
   GO

   -- Get the Size of the ColumnStore_FactResellerSales table
   SELECT
   t.Name AS TableName,
   p.rows AS RowCounts,
   CAST(ROUND((SUM(a.total_pages) / 128.00), 2) AS NUMERIC(36, 2)) AS Size_MB
   FROM sys.tables t
   INNER JOIN sys.indexes i ON t.OBJECT_ID = i.object_id
   INNER JOIN sys.partitions p ON i.object_id = p.OBJECT_ID AND i.index_id = p.index_id
   INNER JOIN sys.allocation_units a ON p.partition_id = a.container_id
   WHERE t.Name = 'ColumnStore_FactResellerSales'
   GROUP BY t.Name, p.Rows
   GO
   ```

6. Select **Execute** on the toolbar to run the query.

7. In the query results, observe the `Size_MB` value of the table before and after the creation of the clustered ColumnStore index. The first value is the size before the index was created, and the second value is the size after the ColumnStore index was created.

   ![The SSMS results pane is displayed, with the size of the ColumnStore_FactResellerSales table highlighted both before and after the creation of the clustered ColumnStore index.](media/ssms-query-results-columnstore-factresellersales-size.png "ColumnStore_FactResellerSales size query results")

8. Create a new query window by selecting **New Query** from the toolbar, and select **Include Actual Execution Plan** by selecting its button in the toolbar.

   ![The Include Actual Execution Plan icon is highlighted on the toolbar.](./media/ssms-toolbar-include-actual-execution-plan.png "Select the Include Actual Execution Plan")

9. Paste the queries below into the new query window, and select **Execute** on the toolbar:

   ```sql
   SELECT productkey, salesamount
   FROM ColumnStore_FactResellerSales

   SELECT productkey, salesamount
   FROM FactResellerSales
   ```

10. In the Results pane, select the **Execution Plan** tab. Check the (relative to the batch) percentage value of the two queries and compare them.

    ![The Execution Plan tab is highlighted in the Results pane, 6% is highlighted for Query 1, and 94% is highlighted for Query 2.](./media/ssms-query-results-execution-plan-columnstore-index.png "Compare the two queries")

11. Run the same queries again, but this time set statistics IO on in the query by adding the following to the top of the query window:

    ```sql
    SET STATISTICS IO ON
    GO
    ```

12. Your query should look like:

    ![The query includes the above information at the top.](./media/ssms-query-statistics-io.png "Set statistics IO")

13. Select **Execute** from the toolbar to run the query.

14. Statistics IO reports on the number of logical pages that are read to return the query results. Select the **Messages** tab of the Results pane, and compare two numbers, logical reads and lob logical reads. You should see a significant drop in the total number of logical reads on the column store-indexed table.

    ![Various information is highlighted on the Messages tab of the Results pane.](./media/ssms-query-results-messages-statistics-io.png "Compare the information")

15. You are now done with the SqlServer2008 VM. Log off of the VM to close the RDP session.

## After the hands-on lab

Duration: 10 minutes

In this exercise, you will delete any Azure resources that were created in support of the lab. You should follow all steps provided after attending the Hands-on lab to ensure your account does not continue to be charged for lab resources.

### Task 1: Delete the resource group

1. Using the [Azure portal](https://portal.azure.com), navigate to the Resource group you used throughout this hands-on lab by selecting Resource groups in the left menu.

2. Search for the name of your research group, and select it from the list.

3. Select Delete in the command bar, and confirm the deletion by re-typing the Resource group name, and selecting Delete.

You should follow all steps provided _after_ attending the Hands-on lab.
