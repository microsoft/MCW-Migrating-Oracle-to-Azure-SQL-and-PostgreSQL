![Microsoft Cloud Workshops](https://github.com/Microsoft/MCW-Template-Cloud-Workshop/raw/main/Media/ms-cloud-workshop.png "Microsoft Cloud Workshops")

<div class="MCWHeader1">
Migrating Oracle to PostgreSQL
</div>

<div class="MCWHeader2">
Hands-on lab step-by-step
</div>

<div class="MCWHeader3">
September 2021
</div>

Information in this document, including URL and other Internet Web site references, is subject to change without notice. Unless otherwise noted, the example companies, organizations, products, domain names, e-mail addresses, logos, people, places, and events depicted herein are fictitious, and no association with any real company, organization, product, domain name, e-mail address, logo, person, place or event is intended or should be inferred. Complying with all applicable copyright laws is the responsibility of the user. Without limiting the rights under copyright, no part of this document may be reproduced, stored in or introduced into a retrieval system, or transmitted in any form or by any means (electronic, mechanical, photocopying, recording, or otherwise), or for any purpose, without the express written permission of Microsoft Corporation.

Microsoft may have patents, patent applications, trademarks, copyrights, or other intellectual property rights covering subject matter in this document. Except as expressly provided in any written license agreement from Microsoft, the furnishing of this document does not give you any license to these patents, trademarks, copyrights, or other intellectual property.

The names of manufacturers, products, or URLs are provided for informational purposes only and Microsoft makes no representations and warranties, either expressed, implied, or statutory, regarding these manufacturers or the use of the products with any Microsoft technologies. The inclusion of a manufacturer or product does not imply endorsement of Microsoft of the manufacturer or product. Links may be provided to third-party sites. Such sites are not under the control of Microsoft and Microsoft is not responsible for the contents of any linked site or any link contained in a linked site, or any changes or updates to such sites. Microsoft is not responsible for webcasting or any other form of transmission received from any linked site. Microsoft is providing these links to you only as a convenience, and the inclusion of any link does not imply endorsement of Microsoft of the site or the products contained therein.

© 2022 Microsoft Corporation. All rights reserved.

Microsoft and the trademarks listed at <https://www.microsoft.com/en-us/legal/intellectualproperty/Trademarks/Usage/General.aspx> are trademarks of the Microsoft group of companies. All other trademarks are property of their respective owners.

**Contents**

- [Migrating Oracle to PostgreSQL hands-on lab step-by-step](#migratingoracletopostgresql-hands-on-lab-step-by-step)
  - [Abstract and learning objectives](#abstract-and-learning-objectives)
  - [Overview](#overview)
  - [Solution architecture](#solution-architecture)
  - [Requirements](#requirements)
  - [Exercise 1: Setup Oracle 18c Express Edition](#exercise-1-setup-oracle-18c-express-edition)
    - [Task 1: Create the Northwind database in Oracle 18c XE](#task-1-create-the-northwind-database-in-oracle-18c-xe)
    - [Task 2: Configure the Starter Application to use Oracle](#task-2-configure-the-starter-application-to-use-oracle)
  - [Exercise 2: Assess the Oracle 18c Database before Migrating to PostgreSQL](#exercise-2-assess-the-oracle-18c-database-before-migrating-to-postgresql)
    - [Task 1: Update Statistics and Identify Invalid Objects](#task-1-update-statistics-and-identify-invalid-objects)
  - [Exercise 3: Prepare to Migrate the Oracle database to PostgreSQL](#exercise-3-prepare-to-migrate-the-oracle-database-to-postgresql)
    - [Task 1: Prepare the PostgreSQL instance using pgAdmin](#task-1-prepare-the-postgresql-instance-using-pgadmin)
    - [Task 2: Create an ora2pg project structure](#task-2-create-an-ora2pg-project-structure)
    - [Task 3: Create a migration report](#task-3-create-a-migration-report)
  - [Exercise 4: Migrate the Database and Application](#exercise-4-migrate-the-database-and-application)
    - [Task 1: Migrate the basic database table schema using ora2pg](#task-1-migrate-the-basic-database-table-schema-using-ora2pg)
    - [Task 2: Migrate Table Data with ora2pg](#task-2-migrate-table-data-with-ora2pg)
    - [Task 3: Finishing the table schema migration](#task-3-finishing-the-table-schema-migration)
    - [Task 4: Migrate Views](#task-4-migrate-views)
    - [Task 5: Migrate the Stored Procedure](#task-5-migrate-the-stored-procedure)
    - [Task 6: Create new Entity Data Models and update the application on the Lab VM](#task-6-create-new-entity-data-models-and-update-the-application-on-the-lab-vm)
    - [Task 7: Update the Dashboard Stored Procedure Call](#task-7-update-the-dashboard-stored-procedure-call)
    - [Task 8: Deploy the application to Azure](#task-8-deploy-the-application-to-azure)
  - [After the hands-on lab](#after-the-hands-on-lab)
    - [Task 1: Delete the resource group](#task-1-delete-the-resource-group)

# Migrating Oracle to PostgreSQL hands-on lab step-by-step

## Abstract and learning objectives

In this hands-on lab, you implement a proof of concept (POC) for conducting a site analysis for a customer to compare cost, performance, and level of effort required to migrate from Oracle to Azure Database for PostgreSQL. You evaluate the dependent applications and reports that need to be updated and come up with a migration plan. Also, you help the customer take advantage of new PostgreSQL features to improve performance and resiliency.

At the end of this hands-on lab, you will be better able to design and build a database migration plan and implement any required application changes associated with changing database technologies.

## Overview

Wide World Importers (WWI) has experienced significant growth in the last few years. As the size of their data grows, they have started to experience issues with their existing Oracle OLTP database, including complex upgrade processes, complex licensing, and even a major failure caused by an overflowing audit table.

The WWI CIO has learned of the many benefits that Azure Database for PostgreSQL provides, including AD support, simple pricing, high performance, and high availability. She is also excited about the many similarities between PL/SQL and PL/pgSQL, since that will reduce the migration effort significantly.

WWI's CIO would like a POC of an OLTP database migration and proof that the new technology will make her company's operations faster, cheaper, and more efficient. 

## Solution architecture

Below is a diagram of the solution architecture you build in this lab. Please study this carefully, so you understand the whole of the solution as you are working on the various components.

![This solution diagram is divided into Microsoft Azure and on-premises. Azure Database for PostgreSQL serves as the primary OLTP database with support for the efficient analysis of JSON data. It is also possible to use the Hyperscale (Citus) offering. Citus supports high availability, which entails pairing two instances to serve as a single node. When one of the instances fails, the other instance--which is kept up to date--is substituted automatically. Logs generated by PostgreSQL's standard logging tools and pgAudit will be stored in Azure Monitor, a tool which allows the analysis of logging data. As for on-premises components, the API app for vendor connections, the Web App for Internet Sales Transactions, and the ASP.NET Core App for inventory management reside locally. BI developers will continue to use Excel and Power BI for reporting.](./media/preferred-solution-architecture-oracle-to-postgres-updated.png "Preferred Solution diagram")

The solution begins by installing and using ora2pg to assess the task of migrating the Oracle XE database supporting the application to Azure Database for PostgreSQL. Then, the tool will be used to migrate the table schema without indexes or constraints to the target. ora2pg will migrate the data to the landing zone. Lastly, remaining objects, including stored procedures and views, will be modified and exported to PostgreSQL through ora2pg. At this point, we will need to modify the MVC application. The first step is to create new entity models. Then, we will scaffold controllers and views from the new models. Lastly, we will use Visual Studio 2019 to deploy the modified app to Azure App Service.

## Requirements

- Microsoft Azure subscription must be pay-as-you-go or MSDN.
  - Trial subscriptions will not work.
- A virtual machine configured with Visual Studio 2019 Community edition.

    >**Note**: If you find that your Visual Studio 2019 VM image comes with Visual Studio 2017, and not 2019, you will need to manually install 2019 Community from [here](https://visualstudio.microsoft.com/downloads/). Ensure that the **ASP.NET and web development** and **Azure development** Workloads are enabled for your installation.

## Exercise 1: Setup Oracle 18c Express Edition

Duration: 45 minutes

In this exercise, you will load a sample database supporting the application. Ensure that you installed Oracle XE, Oracle Data Access Components, and Oracle SQL Developer, as detailed in the Before the Hands-on Lab documents.

### Task 1: Create the Northwind database in Oracle 18c XE

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

9. Repeat steps 7 - 8, replacing the file name in step 26 with each of the following:

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

9. Close the browser to stop debugging the application, and return to Visual Studio.

## Exercise 2: Assess the Oracle 18c Database before Migrating to PostgreSQL

Duration: 15 minutes

In this exercise, you will prepare the existing Oracle database for its migration to PostgreSQL. Preparation involves two main steps. The first step is to update the database statistics. Statistics about the database become outdated as data volumes and activity change over time. Second, you will need to identify invalid objects in the Oracle database. The migration utility will not migrate invalid objects.

### Task 1: Update Statistics and Identify Invalid Objects

1. Run the following statements in SQL Developer.

    ```sql
    -- 21c script
    EXECUTE DBMS_STATS.GATHER_SCHEMA_STATS(ownname => 'NW');
    EXECUTE DBMS_STATS.GATHER_DATABASE_STATS;
    EXECUTE DBMS_STATS.GATHER_DICTIONARY_STATS;
    ```

    >**Note**: This script can take over one minute to run. Ensure that you receive confirmation that the script has executed successfully.

2. Now, we will utilize a query that lists database objects that are invalid and unsupported by the ora2pg utility. It is recommended to fix any errors and compile the objects before starting the migration process.

    ```sql
    SELECT owner, object_type, object_name
    FROM all_objects
    WHERE status = 'INVALID';
    ```

    >**Note**: You should not see any invalid objects. If you have invalid objects, right-click on the correct folder and compile.

    ![The image shows how a user should fix invalid objects.](media/sql-developer-compile-object.png "Fix invalid objects")

## Exercise 3: Prepare to Migrate the Oracle database to PostgreSQL

Duration: 60 minutes

In this exercise, you will create an assessment report that outlines the difficulty of the migration process.

### Task 1: Prepare the PostgreSQL instance using pgAdmin

In this task, we will create the new application user and create the NW database.

1. Launch **pgAdmin** and enter your master password.

2. Under the **Quick Links** section of the Dashboard, there is the option to **Add New Server**. When selected, the **Create - Server** dialog box will open.

3. Under the **General** tab, enter a name for your connection.

    ![Screenshot showing how to enter the connection name.](./media/entering-connection-name.png "Entering the connection name as NorthwindDBConnection")

4. Navigate to the **Connection** tab.

    - You can pull your instance's hostname from the Azure portal (it is available in the resource's overview).
    - For **Username**, enter the admin username available on the instance's overview.
    - For **Password**, enter the admin user password you provided during deployment.
    - Select **Save** when you are ready to connect.

    ![Specifying the database connection.](./media/specifying-db-connection.png "DB connection specifications")

5. If the connection is successful, it should appear under the **Servers** browser dropdown.

    ![Window to show a successful connection.](./media/successful-connection.png "Successful connection")

6. Create a new role, which the application will reference.

    - Under your connection, right-click **Login/Group Roles**.
    - Select **Create > Login/Group Role...**.
    - Name the role **NW**.

7. Under **Definition**, provide a secure password.  

8. Under **Privileges**, change the **Can log in?** slider to the **Yes** position.

    ![Screenshot showing how to define privileges.](./media/nw-defined-privileges.png "Defining Privileges window")

9.  Finally, navigate to **Membership**.

    - Add the **azure_pg_admin** role.
    - Do not select the checkbox next to the role name (this user will not be granting the azure_pg_admin role to others).
    - Select **Save**.

    ![Setting the NW role as a member of the azure_pg_admin role.](./media/set-role-membership-5.4.png "azure_pg_admin role membership")

10. If you did not deploy the lab ARM template, you need to create a new database. Simply right-click the **Databases** dropdown and select **Create > Database...**. Provide `NW` as the database name and the set the owner to the admin user configured in the Azure provisioning step.

Our configuration in pgAdmin is now complete.

### Task 2: Create an ora2pg project structure

**Ora2pg** allows database objects to be exported in multiple files so that is simple to organize and review changes. In this task, you will create the project structure that will make it easy to do this.  

1. Open a command prompt window and navigate to the directory `C:\ora2pg`, where we will create the project structure.

    ```text
    cd C:\ora2pg
    rename ora2pg_conf.dist ora2pg.conf.dist
    ```

2. To create a project, we will use the ora2pg command with the --init_project flag. In the example below, our migration project is titled nw_migration.

    ```cmd
    ora2pg --init_project nw_migration
    ```

    >**Note**: In some cases, ora2pg may fail to find its configuration file. In scenarios such as these, you may need to provide the -c flag with the name of the actual configuration file in your ora2pg directory.

    >**Note**: You may receive an error that ora2pg cannot find Perl. If this is the case, just ensure that `C:\Strawberry\perl\bin` has been added to the PATH variable.

    ```cmd
    ora2pg -c ora2pg_dist.conf --init_project nw_migration
    ```

3. Verify that the command succeeded. There should be a folder with the same name as your migration project in the C:\ora2pg directory.

    ![Screenshot showing the new project in the directory.](./media/ora2pg-new-project.png "New Project in the directory")

4. Navigate to the project directory.

    - Locate **config\ora2pg.conf**.
    - Select the file to open it. If you are asked to select an application to open the file, select **Notepad**.

5. In the **config\ora2pg.conf** file, replace the old values in the file with the correct information.

    ```text
    # Set the Oracle home directory
    ORACLE_HOME	C:\app\demouser\product\21c\dbhomeXE

    # Set Oracle database connection (datasource, user, password)
    ORACLE_DSN	dbi:Oracle:host=LabVM;sid=XE;port=1521
    ORACLE_USER	NW
    ORACLE_PWD	oracledemo123
    ```

    Moreover, you need to populate the schema name correctly.

    ```text
    # Oracle schema/owner to use
    SCHEMA	NW
    ```

6. Confirm that all information entered is correct. The command below should display the version of your local Oracle database.

    ```cmd
    cd nw_migration
    ora2pg -t SHOW_VERSION -c config\ora2pg.conf
    ```

7. We will also need to populate connection information for our Postgre instance. We will use the role we created in the previous task.

    ![Window showing populating connection information.](./media/ora2pg-conf-pgsql.png "Populating connection information")

### Task 3: Create a migration report

The migration report tells us the "man-hours" required to fully migrate to our application and components. The report will provide the user with a relative complexity value. In this task, we will retrieve the migration report for our migration.

1. Navigate to the `C:\ora2pg\nw_migration` directory in command prompt.

2. Ora2pg provides a reporting functionality that displays information about the objects in the existing schema and the estimated effort required to ensure compatibility with PostgreSQL. The command below creates a report titled **6-23-report.html** in the reports folder (when executed within the `C:\ora2pg\nw_migration` directory).

    ```cmd
    ora2pg -c config\ora2pg.conf -t SHOW_REPORT --estimate_cost --dump_as_html > reports\6-23-report.html
    ```

    ![The image shows the objects included in the report.](media/report-created.png "Objects Exported")

    >**Note**: The report displays information for the provided schema--in our case, we placed schema information in `config\ora2pg.conf` before executing the command.

    ![Screenshot showing the Report Schema.](./media/report-schema.png "Report schema")

    >**Note:** The invalid objects count was zero. Also, if the utility assessed database objects, they were listed in the report details.

    ![The image shows an example ora2pg assessment report.](media/report-details.png "Assess Report")

    Of particular interest is the migration level. In our case, it is B-5, which implicates code rewriting, since there are multiple stored procedures that must be altered.

    ![Screenshot showing the Migration level description.](./media/report-migration-level.png "Migration level")

## Exercise 4: Migrate the Database and Application

Duration: 90 minutes

In this exercise, we will begin the migration of the database and the application. This includes migrating database objects, the data, application code, and finally, deploying to Azure App Service.

### Task 1: Migrate the basic database table schema using ora2pg

In this task, we will migrate the database table schema using ora2pg and `psql`, a command-line utility that makes it easy to run SQL files against the database.

Exercise 3 covered planning and assessment steps.  To start the database migration, DDL statements must be created for all valid Oracle objects.

1. In almost all migration scenarios, it is advised that table, index, and constraint schemas are kept in separate files. For data migration performance reasons, constraints should be applied to the target database only after tables are created and data copied. To enable this feature, open **config\ora2pg.conf** file. Set **FILE_PER_CONSTRAINT**, **FILE_PER_INDEX**, **FILE_PER_FKEYS**, and **FILE_PER_TABLE** to 1.

    ![Screenshot showing how to separate table from index and constraints.](./media/separating-table-from-index-and-constraint.png "Separating table from index constraints")

2. Call the following command in the `C:\ora2pg\nw_migration` directory to obtain object schemas (table schemas will be created in a file called **NW-psql.sql**).

    ```cmd
    ora2pg -c config\ora2pg.conf -o NW-psql.sql -t TABLE -b schema\tables\
    ```

    In our scenario, 13 tables are exported. If you see an unreasonably large number, verify that you provided a schema in the configuration file (see step 8 of the previous task). If all was successful, you will see four files in the **schema\tables** directory.

    ![Screenshot showing schema files list.](./media/schema-files.png "Schema files list")

    >**Note**: Open the **schema\tables\NW-psql.sql** file. Notice that all table names are lowercase--using uppercase names for tables and/or columns will require quotations whenever referenced. Furthermore, ora2pg converts data types fairly well. If you have strong knowledge of the stored data, you can modify types to improve performance. You can export individual table schemas in separate files to facilitate the review.

3. Execute the PostgreSQL commands against the PostgreSQL database. You can use any PostgreSQL database client. One way to execute a SQL file against a PostgreSQL database is through the **psql** utility located at the `C:\Program Files\pgAdmin 4\v5\runtime` directory. Just as we did in task 4, append this location to the system PATH variable. Note that you will need to restart your command prompt windows for the change to take effect.

    ![Screenshot showing the process to add psql to the PATH variable.](./media/adding-psql-loc-to-path-x64.png "Adding psql to PATH variable")

4. Reopen the command prompt in the `C:\ora2pg\nw_migration` directory.

    - Enter the following command to run the **NW-psql.sql** file to create tables in the **NW** database.
      - [Server Name] - Enter your Azure PostgreSQL database's DNS name as the value passed to the -h flag. You can find this Server name in the Azure PostgreSQL overview.

        ![The image shows the Azure PostgreSQL overview information. The Server name is circled.](media/azure-database-psql-overview.png "PostgreSQL Server Name")

    - If the connection is successful, you will be asked to enter your password.
    - Then, the command prompt should show a sequence of **CREATE TABLE** statements.

    ```cmd
    cd C:\ora2pg\nw_migration
    psql -U NW@[Server Name] -h [Server Name].postgres.database.azure.com -d NW < schema\tables\NW-psql.sql
    ```

    ![The image shows the create table statements being executed.](media/psql-create-table-results.png "PSQL Create Table Statements")

    >**Note**: If you receive an error like "could not find a 'psql' to execute", use the entire path to the executable in the command (`"C:\Program Files\pgAdmin 4\v5\runtime\psql"`)

    - Navigate to pgAdmin. Refresh the database objects.  Verify the tables exist in pgAdmin.
  
    ![The image shows the newly created tables in the pgAdmin tool.](media/view-tables-in-pgadmin.png "View tables in pgAdmin")

### Task 2: Migrate Table Data with ora2pg

In this Task, we will use the ora2pg utility to migrate table data to the PostgreSQL instance, now that we have created the table schema on the landing zone.

1. Open Explorer and navigate to `C:\ora2pg\nw_migration\config`. Edit the **ora2pg.conf** file. Make sure the PG_DSN, PG_USER, and PG_PWD parameters have the correct values.

    ![The image shows the PG server parameters to connect to Azure PostgreSQL.](media/ora2pg-setup-psql-config.png "Update the PG config")

2. Navigate to `C:\ora2pg\nw_migration\data` in command prompt and enter the following command.

    ```cmd
    cd C:\ora2pg\nw_migration\data
    ora2pg -t COPY -o data.sql -c ..\config\ora2pg.conf
    ```

    You should see the following once the command completes. Notice how all 3,308 rows are accounted for.

    >**Note:** **It may take up to 5 minutes for the export to start**.  If you get authentication errors, double-check your ora2pg config file PG_DSN, PG_USER, and PG_PWD parameters. NW must be in upper case. Capitalization matters.

    ![ora2pg exports all rows in the source Oracle instance.](./media/ora2pg-data-scan.png "All rows exported to SQL files")

### Task 3: Finishing the table schema migration

We migrated the data **before the constraints were applied** to reduce the time required to copy data into the tables. In addition, if foreign keys were present on the target tables, data migration would fail and take longer to import. So, in this task, we will add constraints, foreign keys, and indexes to the target tables. This task assumes that you are in the `C:\ora2pg\nw_migration` directory in command prompt.

1. First, layer on constraints (not foreign keys):

    ```cmd
    cd C:\ora2pg\nw_migration
    psql -U NW@[Server Name] -h [Server Name].postgres.database.azure.com -d NW < schema\tables\CONSTRAINTS_NW-psql.sql
    ```

2. Now, Add foreign keys:

    ```cmd
    psql -U NW@[Server Name] -h [Server Name].postgres.database.azure.com -d NW < schema\tables\FKEYS_NW-psql.sql
    ```

3. Next, layer on the indexes:

    ```cmd
    psql -U NW@[Server Name] -h [Server Name].postgres.database.azure.com -d NW < schema\tables\INDEXES_NW-psql.sql
    ```

4. Navigate to pgAdmin and **Refresh the tables** in the left panel. Verify the indexes and constraints have been applied.

   ![The image shows the indexes for the employees table in pgAdmin.](media/pgadmin-verify-index-constraints-applied.png "Verify indexes in pgAdmin")

5. Before migrating views in the next task, let's verify that table data has been properly migrated. Open **pgAdmin** and connect to the database as the NW user. To use **Query Tool**, select **Query Tool** under the **Tools** dropdown.

    ![Screenshot showing entering the query tool.](./media/entering-query-tool.png "Query Tool highlighted")

    >**Note**: You will need to select the **NW** database before accessing the Query Tool.

6. Enter the following query into the editor:

    ```sql
    SELECT CONCAT(firstname, ' ', lastname) as name,
       territorydescription
    FROM employees e
     JOIN employeeterritories et ON e.employeeid = et.employeeid
     JOIN territories t ON et.territoryid = t.territoryid;
    ```

7. Now, execute the query by selecting the **execution** button on the toolbar.

    ![Screenshot showing running of the query.](./media/running-query.png "Execution button")

8. If you were successful, you should see an output similar to the following. The result set should have 49 rows. It is available under the **Data Output** tab.

    ![Screenshot showing Result set from the select query.](./media/select-query-result-set.png "Query result")

Next, let's take a look at migrating views.

### Task 4: Migrate Views

Views are not referenced by the sample application, but we are including this task here to show you how to do it manually. When we migrate stored procedures, we will show you how to enable an extension that greatly simplifies the migration of objects which reference Oracle-specific functions.  

1. Navigate to the  `C:\ora2pg\nw_migration\schema\views` directory, where we will run ora2pg and psql.

    ```cmd
    cd c:\ora2pg\nw_migration\schema\views
    ora2pg -c ..\..\config\ora2pg.conf -t VIEW -o NW-views.sql
    ```

    >**Note**: Views are exported into individual files. The file specified in the command (NW-views.sql) references the individual files.

    ![The image shows the Oracle views exported to SQL files in Explorer.](media/valid-view-export.png "View SQL Statements")

2. Before we invoke NW-views.sql, we will need to make changes to four files. This is because our application uses a **to_date() function that is not supported in PostgreSQL**. We will need to replace the command in the code with the equivalent **DATE()** function in PostgreSQL.

3. Open **SALES_TOTALS_BY_AMOUNT_NW-views.sql** and replace the existing last line:

    ![Screenshot showing the function that needs to be replaced for sales totals by amounts.](./media/sales-totals-amount-view-old.png "to_date function")

    with this:

    ![Screenshot showing the new view for Sales Total Amounts.](./media/sales-totals-amount-view-new.png "Sales Totals amounts new view")

4. Open **QUARTERLY_ORDERS_NW-views.sql** and replace **to_date(Orders.OrderDate, 'MM/DD/YYYY')** with **DATE(Orders.OrderDate)**.

    >**Note**: The other two applications of the `to_date()` function in that file are acceptable, as seen below.

    ![Testing to_date() function from pgAdmin.](./media/to-date-demo.png "to_date() sample")

5. Open **PRODUCT_SALES_FOR_1997_NW-views.sql** and replace **to_date(Orders.ShippedDate, 'MM/DD/YYYY')** with **DATE(Orders.ShippedDate)**.

6. Open **SALES_BY_CATEGORY_NW-views.sql** and replace **to_date(Orders.OrderDate, 'MM/DD/YYYY')** with **DATE(Orders.OrderDate)**.

7. Now that all modifications are complete, run the NW-views.sql file in psql:

    ```cmd
    psql -U NW@[DB Name] -h [DB Name].postgres.database.azure.com -d NW < NW-views.sql
    ```

8. With that, we have migrated views.

    - Navigate to the **Query Editor** and test these migrated views.
    - Utilize the query below, which will show data where **productsales** is greater than 5000. You can envision how this would be useful in an organization to identify successful items in a given year (1997).

    ```sql
    SELECT *
    FROM product_sales_for_1997
    WHERE productsales > 5000;
    ```

9. When the query is executed, you should see the following result set, with 42 rows. This shows that we have successfully migrated the views.

    ![Result set from query involving the product_sales_for_1997 view.](./media/1997-view-result-set.png "Result set")

Let's migrate stored procedures next.

### Task 5: Migrate the Stored Procedure

Our application utilizes a single stored procedure, so we must be able to migrate it. To do this, we will be using the **orafce** extension, which provides functions that are compatible with Oracle code. We will then call the procedure and view its results using a refcursor.

1. Open `C:\ora2pg\nw_migration\config\ora2pg.conf`. There is a directive titled `PLSQL_PGSQL`. Uncomment it and set the value to `1`. This is necessary for the stored procedure migration.

    ```txt
    # Enable PLSQL to PLPSQL conversion. This is a work in progress, feel
    # free modify/add you own code and send me patches. The code is under
    # function plsql_toplpgsql in Ora2PG/PLSQL.pm. Default enabled.
    PLSQL_PGSQL	1
    ```

2. Only one stored procedure, **NW.SALESBYYEAR**, is in use by the application. So, we will export this stored procedure from the Oracle database for analysis. Run the command below in `C:\ora2pg\nw_migration\schema\procedures`.

    ```cmd
    cd C:\ora2pg\nw_migration\schema\procedures
    ora2pg -c ..\..\config\ora2pg.conf -t PROCEDURE -a SALESBYYEAR -o NW-proc.sql
    ```

3. Open `SALESBYYEAR_NW-proc.sql`. Notice that ora2pg exported the Oracle procedure as a PostgreSQL procedure. In some cases, ora2pg exports procedures as functions. Whether that is acceptable depends on if the object needs to return a value and if transactions must be defined within the object. Note that the exported stored procedure is defined as **SECURITY DEFINER**, removing support for transaction control.

    ![Screenshot showing how to migrate stored procedure using ora2pg.](./media/ora2pg-sp.png "Exporting with ora2pg")

    A second detail to keep in mind is NULLs vs. empty strings. In PostgreSQL, they are handled differently. This is a small distinction in Oracle that can be overlooked, leading to incomplete query results.

4. We will need to edit the procedure's parameter list, and we can do this by using a refcursor. Replace the existing last parameter of the procedure:

    ![Screenshot showing old SP parameter list.](./media/proc-param-list.png "Old parameter list")

    with this:

    ![Screenshot showing new SP parameter list.](./media/proc-param-list-new.png "New parameter list")

    Save your edits.

5. A useful PostgreSQL extension that facilitates greater compatibility with Oracle database objects is **orafce**, which is provided with Azure Database for PostgreSQL. To enable it, navigate to pgAdmin, enter your master password, and connect to your PostgreSQL instance. Then, enter the following command into the query editor and execute it:

    ```sql
    CREATE EXTENSION orafce;
    ```
    
    ![The image shows the create extension command in pgAdmin executed.](media/pgadmin-create-extension.png "pgAdmin create extension")

6. Now, you will need to execute the **NW-proc.sql** file against the PostgreSQL instance.

    ```cmd
    psql -U NW@[Server Name] -h [Server Name].postgres.database.azure.com -d NW < NW-proc.sql
    ```

7. Execute the following statements. Note that pgAdmin requires that each statement be executed independently.

    ```sql
    BEGIN;
    CALL salesbyyear('1996-01-01'::timestamp, '1999-01-01'::timestamp, 'cur_out');
    FETCH ALL FROM cur_out;
    COMMIT;
    ```

8. If all is successful, 809 rows should be returned. The following is an excerpt from the result set, which can be retrieved by executing the FETCH statement.

    ![Screenshot showing result set for salesbyyear stored procedure (1996-1999).](./media/salesbyyear-sp-result-set.png "SP result set after migration example")

### Task 6: Create new Entity Data Models and update the application on the Lab VM

In this task, we will be recreating the ADO.NET data models to accurately represent our PostgreSQL database objects.  

1. On your Lab VM, return to Visual Studio, and open `appsettings.json` from the Solution Explorer.

2. Add a connection string called `PostgreSqlConnectionString`. Ensure that it correctly references the remote Azure Database for PostgreSQL credentials.

   - Replace the value of `Server` with your Azure Database for PostgreSQL DNS name.
   - Substitute the `Server Name`.
   - Verify the value of `Password` is set.

   ```json
   "ConnectionStrings": {
      "OracleConnectionString": "DATA SOURCE=localhost:1521/XE;PASSWORD=oracledemo123;USER ID=NW",
      "PostgreSqlConnectionString": "Server={Server};Database=NW;Port=5432;User Id=NW@{Server Name};Password={Password};Ssl Mode=Require;"
   }
   ```

3. Save the `appsettings.json` file.

    >**Note**: In production scenarios, it is not recommended to store connection strings in files that are checked into version control. Consider using Azure Key Vault references in production and [user secrets](https://docs.microsoft.com/aspnet/core/security/app-secrets) in development.

4. Open the Package Manager console by selecting **Tools** (1), **NuGet Package Manager** (2), and **Package Manager Console** (3).

    ![Opening the Package Manager console in Visual Studio.](./media/open-pmc.png "Opening the Package Manager Console")

5. Enter the following command in the Package Manager console to produce the models. The `-Force` flag eliminates the need to manually clear the `Data` directory. The `Npgsql` connector is included with the sample app to simplify the modification process.

    ```powershell
    Scaffold-DbContext Name=ConnectionStrings:PostgreSqlConnectionString Npgsql.EntityFrameworkCore.PostgreSQL -OutputDir Data -Context DataContext -Schemas public -Force
    ```

    >**Note**: This command will reverse-engineer more tables than are needed. The `-Tables` flag, referencing schema-qualified table names, provides a more accurate approach.

    ![The image shows the entity objects created by the executed command.](media/view-reverse-engineer-table-results.png "Reverse engineered table objects")

6. Attempt to build the solution to identify errors.

    ![The image shows the Visual Studio menu. Build Solution menu item highlighted.](media/visual-studio-build-solution.png "Build Solution")

7. Expand the **Views** folder. Delete the following folders, each of which contains five views:

   - **Customers**
   - **Employees**
   - **Products**
   - **Shippers**
   - **Suppliers**

8. Expand the **Controllers** folder. Delete all controllers, except **HomeController.cs**.

9.  Open **DataContext.cs**. Add the following line to the top of the file, below the other `using` directives.

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

11. Build the solution. Ensure that no errors appear. We added `SalesByYearDbSet` to **DataContext** because **HomeController.cs** references it. We deleted the controllers and their associated views because we will scaffold them again from the models.

    > **Note**: If you encounter build errors, verify that the `_ViewImports.cshtml` file in the `Views` directory was not accidentally deleted. If it was, simply copy the [file from the project GitHub repository](https://raw.githubusercontent.com/microsoft/MCW-Migrating-Oracle-to-Azure-SQL-and-PostgreSQL/master/Hands-on%20lab/lab-files/starter-project/NorthwindMVC/Views/_ViewImports.cshtml).

12. Right-click the **Controllers** folder and select **Add** (1). Select **New Scaffolded Item...** (2).

    ![Adding a new scaffolded item.](./media/add-scaffolded-item.png "New scaffolded item")

13. Select **MVC Controller with views, using Entity Framework**. Then, select **Add**.

    ![Add MVC Controller with Views, using Entity Framework.](./media/add-mvc-with-ef.png "MVC Controller with Views, using Entity Framework")

14. In the **ADD MVC Controller with views, using Entity Framework** dialog box, provide the following details. Then, select **Add**. Visual Studio will build the project.

    - **Model class**: Select `Customer`.
    - **Data context class**: Select `DataContext`.
    - Select all three checkboxes below **Views**.
    - **Controller name**: Keep this set to `CustomersController`.

    ![Scaffolding controllers and views from model classes.](./media/customer-scaffold-views.png "Scaffolding controllers and views")

15. Repeat steps 12-14, according to the following details:

    - **EmployeesController.cs**
      - Based on the **Employee** model class
    - **ProductsController.cs**
      - Based on the **Product** model class
    - **ShippersController.cs**
      - Based on the **Shipper** model class
    - **SuppliersController.cs**
      - Based on the **Supplier** model class

16. Navigate to **Startup.cs**. Ensure that PostgreSQL is configured as the correct provider and the appropriate connection string is referenced in the **ConfigureServices** method. 

    ```csharp
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllersWithViews();

        // Verify this line:
        services.AddDbContext<DataContext>(options => options.UseNpgsql(Configuration.GetConnectionString("PostgreSqlConnectionString")));
    }
    ```

### Task 7: Update the Dashboard Stored Procedure Call

With PostgreSQL, stored procedures cannot return output values without a cursor. This Task details how to write a function to replicate the same logic. Functions can return result sets directly, without a cursor.

1. Open PostgreSQL pgAdmin. Connect to the **NW** database as the **NW** user. Launch the query tool.

2. Into the query tool, copy the following statement and execute it.

    ```sql
    CREATE OR REPLACE FUNCTION SALESBYYEAR_func (p_begin_date TIMESTAMP, p_end_date TIMESTAMP)
	RETURNS TABLE (
		ShippedDate TIMESTAMP,
		OrderID bigint,
		Subtotal double precision,
		Year text
    )
    AS $$
    BEGIN
        RETURN QUERY 
        SELECT Orders.ShippedDate, 
                Orders.OrderID, 
                Order_Subtotals.Subtotal, 
                TO_CHAR(Orders.ShippedDate, 'YY') AS Year
        FROM Orders INNER JOIN Order_Subtotals ON Orders.OrderID = Order_Subtotals.OrderID
        WHERE Orders.ShippedDate Between p_begin_date And p_end_date;
    END;
    $$ 
    LANGUAGE 'plpgsql';
    ```

    ![Creating the SALESBYYEAR_func function in pgAdmin.](./media/sales-by-year-func.png "Creating new function SALESBYYEAR_func")

3. In the Visual Studio solution, navigate to the **HomeController**. Comment out the code under the Oracle comment. First, select the lines for the Oracle code, then select the Comment button in the toolbar.

   ![The code under the Oracle comment is highlighted and labeled 1, and the Comment button in the toolbar is highlighted and labeled 2.](./media/visual-studio-home-controller-comment-out-oracle-lines.png "Comment out code")

4. In the HomeController.cs, add the following `using` references:

    ```csharp
    using NpgsqlTypes;
    using Npgsql;
    ```

5. Below the commented Oracle code and before the LINQ query, add the following:

    ```csharp
    var beginDate = new NpgsqlParameter { ParameterName = "beginDate", NpgsqlDbType = NpgsqlDbType.Timestamp, Direction = ParameterDirection.Input, Value = new NpgsqlDateTime(DateTime.Parse("Jan 1, 1996")) };
    var endDate = new NpgsqlParameter { ParameterName = "endDate", NpgsqlDbType = NpgsqlDbType.Timestamp, Direction = ParameterDirection.Input, Value = new NpgsqlDateTime(DateTime.Parse("Jan 1, 1999")) };

    var salesByYear = await _context.SalesByYearDbSet.FromSqlRaw("SELECT * FROM SALESBYYEAR_func(@beginDate, @endDate);", beginDate, endDate).ToListAsync();
    ```

6. Navigate to the `Models` folder in the Visual Studio Solution. In the `SalesByYear.cs` class, update the type of the `OrderID` property to `long`.

    ```csharp
    public long OrderID { get; set; }
    ```

7. Run the application again by selecting the green Start button in the Visual Studio toolbar.

    ![The Start button is highlighted on the Visual Studio toolbar.](./media/visual-studio-toolbar-start.png "Select Start")

8. Verify the graph is showing correctly on the Northwind Traders dashboard.

    ![The Northwind Traders Dashboard is visible in a browser.](./media/northwind-traders-dashboard.png "View the dashboard")

### Task 8: Deploy the application to Azure

As part of the PoC, the finished app will be hosted on Azure App Service. In this task, you will add a connection string to the App Service resource and use Visual Studio 2019 to complete the deployment.

1. In the Azure portal, navigate to your App Service instance. Navigate to **Configuration** below **Settings**.

2. Below **Connection strings**, select **+ New connection string**. In the **Add/Edit connection string** window, provide the following:

    - **Name**: Use `PostgreSqlConnectionString`.
    - **Value**: Use the connection string from the `appsettings.json` file.
    - **Type**: Select `Custom` (with ASP.NET Core, it is not possible to use the `PostgreSQL` connection string type).

    ![Adding a PostgreSQL connection string to the App Service configuration page.](./media/add-new-connection-string.png "PostgreSQL connection string in App Service")

3. Select **OK** and then select **Save**.
   
4. In Visual Studio's Solution Explorer, right-click the **NorthwindMVC** project (not the solution) and select **Publish...**.

5. The **Publish** window should open. Select **Azure**. Select **Next**.

    ![Screenshot showing the publishing window.](./media/publish-window.png "Selecting Azure in publishing window")

6. Select **Azure App Service (Linux)**. Select **Next**.

7. In the **Publish** window, select your **Subscription name**. Expand the correct resource group and select the App Service resource. Select **Next**.

    ![Selecting the correct App Service instance in the Visual Studio Publish window.](./media/app-service-in-publish-window.png "App Service instance in the Publish window")

8. Select **Publish (generates pubxml file)** for the **Deployment type** tab. Select **Finish**.

    ![Generating a publish profile for the Visual Studio App Service deployment.](./media/pubxml-deployment-type.png "Generating a publish profile")

9. Select **Publish** next to the new publish profile.

10. First, your application will build. Then, all relevant files will be copied into a ZIP archive for deployment.

11. Once the build completes, navigate to your app's link. Test the web application.

    ![Screenshot showing The Northwind app deployed to Azure App Service.](./media/final-northwindapp.png "App deployed to Azure")

    >**Note**: If you still see the default page display, try publishing again.

    >**Note**: Feel free to remove the connection string from the `appsettings.json` file, as it is securely provided to the application through Azure App Service. This is usually done to avoid committing connection strings into version control.

## After the hands-on lab

Duration: 10 minutes

In this exercise, you will delete any Azure resources that were created in support of the lab. You should follow all steps provided after attending the Hands-on lab to ensure your account does not continue to be charged for lab resources.

### Task 1: Delete the resource group

1. Using the [Azure portal](https://portal.azure.com), navigate to the Resource group you used throughout this hands-on lab by selecting Resource groups in the left menu.

2. Search for the name of your research group, and select it from the list.

3. Select Delete in the command bar, and confirm the deletion by re-typing the Resource group name, and selecting Delete.

You should follow all steps provided *after* attending the Hands-on lab.
