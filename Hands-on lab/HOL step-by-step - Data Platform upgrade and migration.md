![Microsoft Cloud Workshops](https://github.com/Microsoft/MCW-Template-Cloud-Workshop/raw/master/Media/ms-cloud-workshop.png "Microsoft Cloud Workshops")

<div class="MCWHeader1">
Data Platform upgrade and migration
</div>

<div class="MCWHeader2">
Hands-on lab step-by-step
</div>

<div class="MCWHeader3">
June 2018
</div>

Information in this document, including URL and other Internet Web site references, is subject to change without notice. Unless otherwise noted, the example companies, organizations, products, domain names, e-mail addresses, logos, people, places, and events depicted herein are fictitious, and no association with any real company, organization, product, domain name, e-mail address, logo, person, place or event is intended or should be inferred. Complying with all applicable copyright laws is the responsibility of the user. Without limiting the rights under copyright, no part of this document may be reproduced, stored in or introduced into a retrieval system, or transmitted in any form or by any means (electronic, mechanical, photocopying, recording, or otherwise), or for any purpose, without the express written permission of Microsoft Corporation.

Microsoft may have patents, patent applications, trademarks, copyrights, or other intellectual property rights covering subject matter in this document. Except as expressly provided in any written license agreement from Microsoft, the furnishing of this document does not give you any license to these patents, trademarks, copyrights, or other intellectual property.

The names of manufacturers, products, or URLs are provided for informational purposes only and Microsoft makes no representations and warranties, either expressed, implied, or statutory, regarding these manufacturers or the use of the products with any Microsoft technologies. The inclusion of a manufacturer or product does not imply endorsement of Microsoft of the manufacturer or product. Links may be provided to third party sites. Such sites are not under the control of Microsoft and Microsoft is not responsible for the contents of any linked site or any link contained in a linked site, or any changes or updates to such sites. Microsoft is not responsible for webcasting or any other form of transmission received from any linked site. Microsoft is providing these links to you only as a convenience, and the inclusion of any link does not imply endorsement of Microsoft of the site or the products contained therein.

© 2018 Microsoft Corporation. All rights reserved.

Microsoft and the trademarks listed at <https://www.microsoft.com/en-us/legal/intellectualproperty/Trademarks/Usage/General.aspx> are trademarks of the Microsoft group of companies. All other trademarks are property of their respective owners.

## Contents

- [Data Platform upgrade and migration hands-on lab step-by-step](#data-platform-upgrade-and-migration-hands-on-lab-step-by-step)
  - [Abstract and learning objectives](#abstract-and-learning-objectives)
  - [Overview](#overview)
  - [Solution architecture](#solution-architecture)
  - [Requirements](#requirements)
  - [Exercise 1: Deploy SQL Server instances](#exercise-1-deploy-sql-server-instances)
    - [Task 1: Install SQL Server 2017](#task-1-install-sql-server-2017)
    - [Task 2: Install SQL Server 2008 R2](#task-2-install-sql-server-2008-r2)
    - [Task 3: Install AdventureWorks sample database](#task-3-install-adventureworks-sample-database)
    - [Task 4: Update SQL Server settings using Configuration Manager](#task-4-update-sql-server-settings-using-configuration-manager)
    - [Task 5: Add inbound port rule](task-5-add-inbound-port-rule)
    - [Task 6: Copy the SqlServerDw VM IP address](task-6-copy-the-sqlserverdw-vm-ip-address)
  - [Exercise 2: Migrate SQL Server to Azure SQL Database using DMS](#exercise-2-migrate-sql-server-to-azure-sql-database-using-dms)
    - [Task 1: Register the Microsoft DataMigration resource provider](#task-1-register-the-microsoft-datamigratin-resource-provider)
    - [Task 2: Create Azure Database Migration Service](#task-2-create-azure-database-migration-service)
    - [Task 3: Assess the on-premises database](#task-3-assess-the-on-premises-database)
    - [Task 4: Migrate the database schema](#task-4-migrate-the-database-schema)
    - [Task 5: Create a migration project](#task-5-create-a-migration-project)
    - [Task 6: Run the migration](#task-6-run-the-migration)
    - [Task 7: Verify data migration](#task-7-verify-data-migration)
  - [Exercise 3: Post upgrade enhancement](#exercise-3-post-upgrade-enhancement)
    - [Task 1: Table compression](#task-1-table-compression)
    - [Task 2: Clustered ColumnStore index](#task-2-clustered-columnstore-index)
  - [Exercise 4: Setup Oracle 11g Express Edition](#exercise-4-setup-oracle-11g-express-edition)
    - [Task 1: Install Oracle XE](#task-1-install-oracle-xe)
    - [Task 2: Install Oracle Data Access components](#task-2-install-oracle-data-access-components)
    - [Task 3: Install SQL Server Migration Assistant for Oracle](#task-3-install-sql-server-migration-assistant-for-oracle)
    - [Task 4: Install dbForge Fusion tool](#task-4-install-dbforge-fusion-tool)
    - [Task 5: Create the Northwind database in Oracle 11g XE](#task-5-create-the-northwind-database-in-oracle-11g-xe)
    - [Task 6: Configure the Starter Application to use Oracle](#task-6-configure-the-starter-application-to-use-oracle)
  - [Exercise 5: Migrate the Oracle database to SQL Server 2017](#exercise-5-migrate-the-oracle-database-to-sql-server-2017)
    - [Task 1: Create Northwind database](#task-1-create-northwind-database)
    - [Task 2: Migrate the Oracle database to SQL Server 2017 using SSMA](#task-2-migrate-the-oracle-database-to-sql-server-2017-using-ssma)
  - [Exercise 6: Migrate the Application](#exercise-6-migrate-the-application)
    - [Task 1: Create a new Entity Model against SQL Server](#task-1-create-a-new-entity-model-against-sql-server)
    - [Task 2: Modify Application Code](#task-2-modify-application-code)
  - [After the hands-on lab](#after-the-hands-on-lab)
    - [Task 1: Delete the resource group](#task-1-delete-the-resource-group)

# Data Platform upgrade and migration hands-on lab step-by-step

If you have not yet completed the steps to set up your environment in [Before the hands-on lab](https://github.com/Microsoft/MCW-Data-Platform-upgrade-and-migration/blob/master/Hands-on%20lab/Before%20the%20HOL%20-%20Data%20Platform%20upgrade%20and%20migration%20.md), you will need to do that before proceeding.

## Abstract and learning objectives

In this hands-on lab, you will implement a proof of concept (POC) for conducting a site analysis for a customer to compare cost, performance, and level of effort required to migrate from Oracle to SQL Server. You will evaluate the dependent applications and reports that will need to be updated and come up with a migration plan. In addition, you will help the customer take advantage of new SQL Server features to improve performance and resiliency, as well as conduct a migration from an old version of SQL Server to Azure SQL Database.

At the end of this hands-on lab, you will be better able to design and build a database migration plan and implement any required application changes associated with changing database technologies.

## Overview

World Wide Importers (WWI) has experienced significant growth in the last few years. In addition to predictable growth, they’ve had a substantial amount of growth in the data they store in their data warehouse. Their data warehouse is starting to show its age; slowing down during extract, transform, and load (ETL) operations and during critical queries. It was built on SQL Server 2008 R2 Standard Edition.

The WWI CIO has recently read about new performance enhancements of Azure SQL Database and SQL Server 2017. She is excited about the potential performance improvements related to clustered ColumnStore indexes. She is also hoping that table compression will improve performance and backup times.

WWI is concerned about upgrading their database to Azure SQL Database or SQL Server 2017. The data warehouse has been successful for a long time. As it has grown, it has filled with data, stored procedures, views, and security. WWI wants assurance that if it moves its data store, it won’t run into any incompatibilities with the storage engine of Azure SQL Database or SQL Server 2017.

WWI’s CIO would like a POC of a data warehouse move and proof that the new technology will help ETL and query performance.

## Solution architecture

Below is a diagram of the solution architecture you will build in this lab. Please study this carefully, so you understand the whole of the solution as you are working on the various components.

![This solution diagram is divided in to Microsoft Azure, and On Premises. Microsoft Azure includes SQL Server 2017 in a VM as an Always On Secondary, and Azure SQL Data Warehouse for a stretch table. On Premise includes the following elements: API App for vendor connections; Web App for Internet Sales Transactions; ASP.NET Core App for inventory management; SQL Server 2017 OLTP for Always On and JSON store; SSRS 2017 for Reporting of OLTP, Data Warehouse, and Cubes; SSIS 2017 for a Data Warehouse Load; Excel for reporting; SQL Server 2017 Enterprise for a Data Warehouse; and SSAS 2017 for a Data Warehouse. ](./media/preferred-solution-architecture.png "Preferred Solution diagram")

The solution begins with using the Microsoft Data Migration Assistant to perform an assessment to see what potentials issues need to be addressed in upgrading the database to SQL Server 2017 or Azure SQL Database. After correcting any issues, the SQL Server 2008 database is migrated to Azure SQL Database, using the Azure Database Migration Service. Two features of Azure SQL Database, Table Compression and ColumnStore Index, will be applied to demonstrate value and performance improvements from the upgrade. For the ColumnStore Index, a new table based on the existing FactResellerSales table will be created, and a ColumnStore index applied. Next, the Oracle XE database supporting the application will be migrated to an on-premises SQL Server 2017 Enterprise instance using SQL Server Migration Assistant (SSMA) 7.x for Oracle. Once the Oracle database has been migrated, the NorthwindMVC application will be updated, so it targets SQL Server 2017 instead of Oracle. The entity models are updated against SQL Server, and code updates are made to use the new Entity Framework context based on SQL Server. Corrections to stored procedures are made due to differences in how stored procedures are accessed in Oracle versus SQL Server.

## Requirements

- Microsoft Azure subscription must be pay-as-you-go or MSDN
  - Trial subscriptions will not work
- A virtual machine configured with:
  - Visual Studio Community 2017 or later
  - Azure SDK 2.9 or later (Included with Visual Studio 2017)

## Exercise 1: Deploy SQL Server instances

Duration: 60 minutes

In this exercise, you will install and configure SQL Server 2017 and SQL Server 2008 R2 on the SqlServerDw VM. The databases on this VM will act as the "on-premises" databases for this hands-on lab.

### Task 1: Install SQL Server 2017

In this task, you will install SQL Server 2017 and Microsoft SQL Server Management Studio (SSMS) on the SqlServerDw VM.

> Connect to the SqlServerDw VM following the steps provided in Task 5 of the [Before the hand-on lab](./Before%20the%20HOL%20-%20Data%20Platform%20upgrade%20and%20migration.md) exercises.

1. On the SqlServerDw VM, open a web browser and navigate to the [SQL Server 2017 Developer edition download](https://www.microsoft.com/sql-server/sql-server-downloads) page, then select the **Download now** link under Developer in the free specialized edition section

    ![The Download now link is highlighted under Developer edition at https://www.microsoft.com/sql-server/sql-server-downloads.](media/sql-server-2017-developer-edition-download.png "Download SQL Server 2017 Developer Edition")

2. Run the downloaded installer

3. In the install dialog, select **Custom** as the installation type on the first screen

    ![In the installation dialog box, Custom is selected and highlighted under Select an installation type.](./media/sql-server-2017-install-type.png "Select Custom")

4. On the next screen, leave the Media Location set to the default value, and select **Install**

    ![The default value (C:\\SQLServer2017Media) is listed under Media Location, and Install is highlighted at the bottom.](./media/sql-server-2017-install-location.png "Install with the default value")

5. Once the necessary components are downloaded, the SQL Server Installation Center will open. Select **Installation** on the left-hand menu, then select **New SQL Server stand-alone installation or add features to an existing installation**.

    ![Installation is highlighted on the left side of the SQL Server Installation Center dialog box. At right, New SQL Server stand-alone installation or add features to an existing installation is highlighted.](./media/sql-server-2017-installation-center-installation-new-sql-server.png "Choose your installation")

6. On the Product Key screen, select **Developer** under Specify a free edition, and select **Next**

    ![Developer displays under Specify a free edition.](./media/sql-server-2017-installation-center-installation-new-sql-server-free-edition.png "Specify the edition")

7. On the License Terms screen, check the box to **accept the license terms**, and select **Next**

8. Select **Next** on each the following screens to accept the defaults, until you get to the Feature Selection screen

9. On the **Feature Selection** screen, check the box next to **Database Engine Services**, and select **Next**

    ![Database Engine Services is selected and highlighted under Instance Features on the Feature Selection screen.](./media/sql-server-2017-installation-center-feature-selection.png "Select Database Engine Services")

10. On the **Instance Configuration** screen, leave **Default instance** selected, and select **Next**

11. Accept the default values on the **Server Configuration** screen, and select **Next**

12. On the **Database Engine Configuration** screen:

    - Select **Mixed Mode** for the Authentication Mode

    - Enter **Password.1!!** for the sa password

    - Click the **Add Current User** button to make the **demouser** windows account a SQL Server administrator

    ![The information above is highlighted on the Database Engine Configuration screen, including the Add Current User button under Specify SQL Server administrators.](./media/sql-server-2017-installation-center-database-engine-configuration.png "Specify Database Engine Configuration settings")

    - Select **Next**

13. Select **Install** on the Ready to Install screen to start the installation

14. Select **Close** when the installation is complete

15. Back in the SQL Server Installation Center dialog, select **Install SQL Server Management Tools** on the Installation tab

    ![Installation is highlighted on the left side of the SQL Server Installation Center dialog box. At right, Install SQL Server Management Tools is highlighted.](./media/sql-server-2017-installation-center-installation-ssms.png "Select Install SQL Server Management Tools")

16. In the browser window that opens, scroll down and select the **Download SQL Server Management Studio 17.x** link, then run the installer

    ![Download SQL Server Management Studio 17.7 is highlighted.](media/ssms-dowload.png "SSMS Download link")

17. In the install window that appears, select Install to complete the installation

    ![Install is highlighted at the bottom of the Microsoft SQL Server Management Studio installation window.](media/ssms-install.png "Microsoft SQL Server Management Studio Install Welcome page")

18. Close the SQL Server Management Studio (SSMS) installer once setup is completed

19. Close the SQL Server Installation Center dialog

### Task 2: Install SQL Server 2008 R2

In this task, you will install SQL Server 2008 R2 as a Named Instance on the SqlServerDw VM.

1. On the SqlServerDw VM, open a web browser, and navigate to <https://www.microsoft.com/download/details.aspx?id=30438>, and select the **Download** link on the page

    ![Download is selected and highlighted on the https://www.microsoft.com/download/details.aspx?id=30438 page under Microsoft SQL Server 2008 R2 SP2 * Express Edition.](./media/sql-server-2008-r2-express-edition-download.png "Download link")

2. Download SQL Server 2008 R2 Express with Advanced Services by selecting **SQLEXPRADV_x64_ENU.exe**, then selecting **Next**

    ![SQLEXPRADV_x64_ENU.exe is highlighted in the list under Choose the download you want.](./media/sql-server-2008-r2-express-edition-download-files.png "Download SQL Server 2008 R2 Express with Advanced Services")

3. Run the installer

4. In the SQL Server Installation Center window, select **New installation or add features to an existing installation**

    ![New installation or add features to an existing installation is highlighted in the SQL Server Installation Center window.](./media/sql-server-2008-installation-center-installation-new-installation.png "Select New installation or add features to an existing installation")

5. In the SQL Server 2008 R2 Setup dialog, accept the license terms on the License Terms screen, and select **Next**

6. Accept the default values on each screen by selecting **Next**, until you get to the Instance Configuration screen

7. On the **Installation Configuration** screen, select **Named Instance**, and enter **SQLServer2008** for the instance name, then select **Next**

    ![Instance Configuration is highlighted on the left side of the SQL Server 2008 R2 Setup dialog box. At right, Named instance: SQLServer2008 is highlighted.](./media/sql-server-2008-installation-center-instance-configuration.png "Enter the instance name")

8. On the **Server Configuration** screen, select **Use the same account for all SQL Server services**

    ![Server Configuration is highlighted on the left side of the SQL Server 2008 R2 Setup dialog box. At right, SQL Server Database Engine is selected on the Service Accounts tab, and Use the same account for all SQL Server services is highlighted at the bottom.](./media/sql-server-2008-installation-center-server-configuration.png "Select Use the same account for all SQL Server services")

9. Select **Browse**, and enter **demouser**. Select the **demouser** account you created when you provisioned the VM, then enter the password, **Password.1!!**, and select **OK**

    ![The credentials above are entered in the Use the same account for all SQL Server 2008 R2 services dialog box.](media/sql-server-2008-installation-center-service-account.png "Service account credentials")

10. Select Next on the Server Configuration screen

11. On the Database Engine Configuration screen:

    - Select **Mixed Mode**

    - Enter **Password.1!!** for the sa password

    - Click the **Add Current User** button to specify the demouser Windows account as a SQL Server Administrator

        ![Database Engine Configuration is highlighted on the left side of the SQL Server 2008 R2 Setup dialog box. The information above is highlighted at right, including the Add Current User button under Specify SQL Server administrators.](media/sql-server-2008-installation-center-database-engine-configuration.png "Specify Database Engine Configuration settings")

    - Select **Next**

12. Select **Next** to accept the default values on the remaining screens to complete the installation

13. Select **Close** on the SQL Server 2008 R2 Setup dialog when the installation is complete

14. Close the SQL Server 2008 R2 Installation Center window

### Task 3: Install AdventureWorks sample database

In this task, you will install the AdventureWorks database in SQL 2008 R2. It will act as the "on-premises" data warehouse database that you will migrate to Azure SQL Database.

1. On the SqlServerDw VM, open a web browser, and navigate to the GitHub site containing the sample AdventureWorks 2008 R2 database at <https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks2008r2>

2. Scroll down under Assets, and select `adventure-works-2008r2-dw.script.zip`

    ![The adventure-works-2008r2-dw-script.zip download link is highlighted under Assets for the sample database.](./media/adventure-works-2008-r2-sample-download.png "Assets list")

3. Save the file, and unzip the downloaded file to a folder you create, called `C:\AdventureWorksSample`

4. Launch **SQL Server Management Studio 17** (SSMS), found under Start->Apps->Microsoft SQL Server Tools 17.

    > Note: There are two version of SSMS installed, so make sure you open SSMS 17.

    ![SQL Server Management Studio 17 (SSMS) is highlighted under Microsoft SQL Server Tools 17.](./media/ssms-start-menu.png "Open SQL Server Management Studio 17 (SSMS)")

5. In the Connect to Server dialog, enter **SqlServerDw\SQLSERVER2008** in the Server name box, leave Authentication set to **Windows Authentication**, and select **Connect**

    ![In the Connect to Server dialog box, SqlServerDw\SQLSERVER2008 is highlighted in the Server name box.](./media/ssms-connect-to-sqlserver2008.png "Connect to SqlServerDw\SQLSERVER2008")

6. In SSMS, select the **Open File** icon in SSMS menu bar

    ![The File icon is highlighted on the SSMS menu bar.](./media/ssms-toolbar-open-file.png "Select Open File")

7. In the Open File dialog, browse to the `C:\AdventureWorksSample\AdventureWorks 2008R2 Data Warehouse\` folder, select the file named `instawdwdb.sql`, and then select **Open**

    ![Local Disk (C:) is selected on the left side of the Open File dialog box, and instawdwdb.sql is selected and highlighted on the right.](./media/ssms-open-file-dialog.png "Select instawdwdb.sql")

8. Next, select **Tools** in the SSMS menu, then select **Options**

    ![Tools is highlighted on the SSMS menu bar, and Options is highlighted at the bottom.](./media/ssms-tools-options-dialog.png "Select Options")

9. In the Options dialog, expand **Text Editor** in the tree view on the left, then expand **Transact-SQL**, select **General**, then check the box next to **Line numbers**. This will display line numbers in the query editor window, to make finding the lines specified below easier.

    ![On the left side of the Options dialog box, Text Editor is highlighted, Transact-SQL is highlighted below that, and General is selected and highlighted below that. At right, Line numbers is selected and highlighted.](./media/ssms-tools-options-text-editor-tsql-general.png "Display line numbers in the query editor")

10. Select **OK** to close the Options dialog

11. In the SSMS query editor for `instawdwdb.sql`, uncomment the `SETVAR` lines (lines 36 and 37) by removing the double hyphen "--" from the beginning of each line

12. Next, edit the file path for each variable so they point to the following (remember to include a trailing backslash "\" on each path):

    - SqlSamplesDatabasePath: `C:\AdventureWorksSample\`

    - SqlSamplesSourceDataPath: `C:\AdventureWorksSample\AdventureWorks 2008R2 Data Warehouse\`

        ![The variables and file paths specified above are highlighted in the SSMS query editor.](./media/ssms-query-editor-instawdwdb.png "Edit the variable file paths")

13. Place SSMS into **SQLCMD mode** by selecting it from the **Query** menu

    ![SQLCMD Mode is highlighted in the Query menu.](./media/ssms-query-menu-sqlcmd-mode.png "Select SQLCMD Mode")

14. Execute the script by selecting the **Execute** button on the toolbar in SSMS

    ![Execute is highlighted on the SSMS toolbar.](./media/ssms-toolbar-execute.png "Run the script")

15. This will create the `AdventureWorksDW2008R2` database. When the script is done running, you will see output similar to the following in the results pane.

    ![Output is displayed in the results pane. At this time, we are unable to capture all of the information in the window. Future versions of this course should address this.](./media/ssms-query-instawdwdb-script-output.png "View the script output")

16. Expand **Databases** in Object Explorer, right-click the `AdventureWorksDW2008R2` database, and select **Rename**

    ![On the left side of Object Explorer, Databases is highlighted, AdventureWorksDW2008R2 is highlighted below that, and Rename is selected and highlighted in the submenu.](./media/ssms-databases-rename.png "Select Rename")

17. Set the name of the database to `WorldWideImporters`

    ![WorldWideImporters is highlighted under Databases in Object Explorer.](./media/ssms-databases-worldwideimporters.png "Name the database")

### Task 4: Update SQL Server settings using Configuration Manager

In this task, you will update the SQL Server service accounts and other settings associated with the SQL Server instances installed on the VM.

1. From the Start Menu on your SqlServerDw VM, search for **SQL Server 2017 Configuration Manager**, then select it from the search results

    ![SQL Server 2017 Configuration Manager is selected and highlighted in the Search results.](./media/windows-2012-search-sql-server-2017-configuration-manager.png "Select SQL Server 2017 Configuration Manager")

2. From the tree on the left of the Configuration Manager window, select **SQL Server Services**

    ![SQL Server Services is highlighted on the left side of SQL Server 2017 Configuration Manager.](./media/sql-server-2017-configuration-manager-sql-server-services.png "Select SQL Server Services")

3. In the list of services, double-click **SQL Server (MSSQLSERVER)** to open its properties dialog

    ![SQL Server (MSSQLSERVER) is highlighted in the list on the right side of SQL Server 2017 Configuration Manager.](media/sql-server-2017-configuration-manager-sql-server-mssqlserver.png "Select SQL Server (MSSQLSERVER)")

4. In the SQL Server (MSSQLSERVER) Properties dialog, change the **Log On user** to use the demouser account, by entering **demouser** into the Account Name box, then entering the password, **Password.1!!**, into the Password and Confirm password boxes

    ![The above credentials are highlighted in the SQL Server (MSSQLSERVER) Properties dialog box.](media/sql-server-2017-configuration-manager-sql-server-mssqlserver-properties.png "Enter demouser credentials")

5. Select **OK**

6. Select **Yes** to restart the service in the **Confirm Account Change** dialog

7. Repeat steps 3 - 6 above to set the server account to demouser for the **SQL Server (SQLSERVER2008)** instance as well, if it is not already using the demouser account

8. While still in the SQL Server 2017 Configuration Manager, expand **SQL Server Network Configuration**, select **Protocols for MSSQLSERVER**, and double-click **TCP/IP** to open the properties dialog

    ![Protocols for MSSQLSERVER is highlighted on the left side of SQL Server 2017 Configuration Manager, and TCP/IP is highlighted in the Protocol Name list on the right.](./media/sql-server-2017-configuration-manager-protocols-for-mssqlserver.png "Select TCP/IP")

9. On the TCP/IP Properties dialog, set **Enabled** to **Yes**, and select **OK**

    ![Enabled is selected on the Protocol tab of the TCP/IP Properties dialog box.](./media/sql-server-2017-configuration-manager-protocols-tcp-ip-properties.png "Enable TCP/IP")

10. When prompted that the changes will not take effect until the service is restarted, select **OK**. You will restart the service later.

11. Now, select **Protocols for SQLSERVER2008**, double-click **TCP/IP** to open the properties dialog

12. As you did above, set **Enabled** to **Yes**

13. Next, select the **IP Addresses** tab within the TCP/IP Properties dialog

14. On the IP Addresses tab, scroll down to the IPAll section, and note the port number assigned in the **TCP Dynamic Ports** field. Copy the value into a text editor, such as Notepad, for later use. SQL Server 2008 R2 was installed as a Named Instance, so it was assigned a dynamic port number, different than 1433 which is typically assigned to SQL Server instances. You will need this port number to create an inbound port rule for the SqlServerDw VM to allow the Azure Database Migration Service to access the database.

    ![The IP Addresses tab of the TCP/IP Properties dialog is displayed, with TCP Dynamic Ports highlighted under the IPAll section.](media/tcp-ip-properties-ip-addresses-tab.png "TCP/IP Properties IP Addresses tab")

15. Select **OK** to close the TCP/IP Properties dialog. When prompted that the changes will not take effect until the service is restarted, select **OK**. You will restart the service later.

16. As done previously, select **SQL Server Services** in the tree on the left, then right-click **SQL Server (MSSQLSERVER)** in the services pane, and select **Restart**

    ![SQL Server Services is highlighted on the left side of SQL Server 2017 Configuration Manager, SQL Server (MSSQLSERVER) is highlighted on the right, and Restart is highlighted in the submenu.](./media/sql-server-2017-configuration-manager-sql-server-services-sql-server-restart.png "Select Restart")

17. Repeat the previous step for the **SQL Server (SQLSERVER2008)** service

18. Repeat the previous step for the **SQL Server Agent (MSSQLSERVER)** service, this time selecting **Start** from the menu

    ![SQL Server Services is highlighted on the left side of SQL Server 2017 Configuration Manager, SQL Server Agent (MSSQLSERVER) is highlighted on the right, and Start is highlighted in the submenu.](./media/sql-server-2017-configuration-manager-sql-server-services-sql-server-agent-restart.png "Select Start")

19. Close the SQL Server 2017 Configuration Manager

### Task 5: Add inbound port rule

In this task, you will add an inbound port rule for the SqlServerDw VM, to allow the dynamic port used by the SQL Server 2008 R2 Named Instance.

1. In the [Azure portal](https://portal.azure.com), navigate to your SqlServerDw VM

2. On the SqlServerDw blade, select **Networking** in the left-hand menu, under Settings, and then select **Add inbound port rule**

    ![On the SqlServerDw VM blade, Networking is selected and highlighted under Settings in the left-hand menu, and the Add inbound port rule button is highlighted on the Networking blade.](media/sql-virtual-machine-add-inbound-port-rule.png "Virutal Machine Networking blade")

3. In the Add inbound security rule dialog, enter the following:

    - **Destination port ranges**: Enter the dynamic port you noted and copied for your SQL Server 2008 instance

    - **Name**: Enter Port_[DYNAMIC-PORT-NUMBER], such as Port_49700

    - Leave all other settings set to the default values, and select **Add**

        ![In the Add inbound security rule dialog, the values specified above are entered into the appropriate fields.](media/sql-virtual-machine-networking-add-inbound-security-rule.png "Add inbound security rule")

### Task 6: Copy the SqlServerDw VM IP address

In this task, you will copy the IP address for later reference.

1. In the [Azure portal](https://portal.azure.com), navigate to your SqlServerDw VM

2. On the SqlServerDw overview blade, select the copy button next to the Public IP address value, and paste the value into a text editor, such as Notepad, for later reference

    ![On the SqlServerDw VM Overview blade, the Public IP address is highlighted.](media/sql-virtual-machine-overview-blade-ip-address.png "Virtual machine Overview")

## Exercise 2: Migrate SQL Server to Azure SQL Database using DMS

Duration: 60 minutes

World Wide Importers would like a Proof of Concept (POC) that moves their database warehouse to Azure SQL Database. They would like to know about any incompatible features that will block their eventual production move. In this exercise, you will use the [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) (DMS) to migrate the WorldWideImporters database from the "on-premises" SQL Server 2008 R2 instance to [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/).

### Task 1: Register the Microsoft DataMigration resource provider

In this task, you will register the Microsoft.DataMigration resource provider with your subscription in Azure.

1. In the [Azure portal](https://portal.azure.com), select **All services** from the Azure navigation pane, and then select **Subscriptions**

    ![All services is highlighted in the Azure navigation pane, and Subscriptions is highlighted in the All services blade.](media/azure-portal-all-services-subscriptions.png "Azure All services blade")

2. Select the subscription you are using for this hands-on lab from the list, select **Resource providers**, enter "migration" into the filter box, and then select **Register** next to Microsoft.DataMigration

    ![The Subscription blade is displayed, with Resource providers selected and highlighted under Settings. On the Resource providers blade, migration is entered into the filter box, and Register is highlighted next to Microsoft.DataMigration.](media/azure-portal-subscriptions-resource-providers-register-microsoft-datamigration.png "Resource provider registration")

### Task 2: Create Azure Database Migration Service

In this task, you will provision an instance of the Azure Database Migration Service (DMS).

1. In the [Azure portal](https://portal.azure.com/), select **+Create a resource**, enter "database migration" into the Search the Marketplace box, select **Azure Database Migration Service** from the results, and select **Create**

    ![+Create a resource is selected in the Azure navigation pane, and "database migration" is entered into the Search the Marketplace box. Azure Database Migration Service is selected in the results.](media/create-resource-azure-database-migration-service.png "Create Azure Database Migration Service")

2. On the Create Migration Service blade, enter the following:

    - **Service Name**: Enter wwi-dms

    - **Subscription**: Select the same subscription you are using for this hands-on lab

        > NOTE: If you see the message `Your subscription doesn't have proper access to Microsoft.DataMigration`, refresh the browser window before proceeding. If the message persists, verify you successfully registered the resource provider, and then you can safely ignore this message.

    - **Resource Group**: Choose Use existing, and select the hands-on-lab-SUFFIX resource group

    - **Virtual network**: Choose Create new, and enter wwi-dms-vnet

    - **Location**: Select the location you are using for resources in this hands-on lab

    - **Pricing tier**: Select Basic: 1 vCore

        ![The Create Migration Service blade is displayed, with the values specified above entered into the appropriate fields.](media/create-migration-service.png "Create Migration Service")

3. Select **Create**

4. It can take 15 minutes to deploy the Azure Data Migration Service, so continue to the next task while the deployment completes

### Task 3: Assess the on-premises database

World Wide Importers would like an assessment to see what potential issues they would have to address in moving their database to Azure SQL Database.

1. On the SqlServerDw VM, select the **Download** button on the [Data Migration Assistant v3.x](https://www.microsoft.com/download/details.aspx?id=53595) page, and run the downloaded installer

2. Select **Next** on each of the screens, accepting to the license terms and privacy policy in the process

3. Select **Install** on the Privacy Policy screen to begin the installation

4. On the final screen, check the **Launch Microsoft Data Migration Assistant** check box, and select **Finish**

    ![Launch Microsoft Data Migration Assistant is selected and highlighted at the bottom of the Microsoft Data Migration Assistant Setup dialog box.](./media/data-migration-assistant-setup-finish.png "Run the Microsoft Data Migration Assistant")

5. In the Data Migration Assistant window, select the New **(+)** icon in the left-hand menu

    ![+ New is selected and highlighted in the Data Migration Assistant window.](./media/data-migration-assistant-new-project.png "Select + New")

6. In the New project dialog, enter the following:

    - **Project type**: Select Assessment

    - **Project name**: Enter Assessment

    - **Source server type**: SQL Server

    - **Target server type**: Azure SQL Database

        ![The above information is entered in the New project dialog box.](./media/data-migration-assistant-new-project-assessment.png "Enter information in the New project dialog box")

    - Select **Create**

7. On the **Options** tab, ensure the **Check database compatibility** and **Check feature parity** report types are checked, and select **Next**

    ![Check database compatibility and Check feature parity are selected and highlighted on the Options screen.](./media/data-migration-assistant-options.png "Select the report types")

8. In the **Connect to a server** dialog on the **Select sources** tab, enter `SqlServerDw\SQLSERVER2008` into the Server name box, and **uncheck Encrypt connection**, then select **Connect**

    ![In the Connect to a server dialog box, SqlServerDw\SQLSERVER2008 is highlighted in the Server name box, and Encrypt connection is unchecked and highlighted below that in the Connect to a server dialog box.](./media/data-migration-assistant-select-sources-sqlserver2008.png "Enter information in the Connect to a server dialog box")

9. In the **Add sources** dialog that appears, check the box next to **WorldWideImporters**, and select **Add**

    ![WorldWideImporters is selected and highlighted under SqlServerDw\SQLSERVER2008 in the Add sources dialog box.](./media/data-migration-assistant-select-sources-sqlserver2008-worldwideimporters.png "Select WorldWideImporters")

10. Select **Start Assessment**

11. Review the Assessment results, selecting both **SQL Server feature parity** and **Compatibility issues** options and viewing the reports

    ![Various information is selected on the Review results screen. At this time, we are unable to capture all of the information in the window. Future versions of this course should address this.](./media/data-migration-assistant-review-results-sqlserver2008-worldwideimporters.png "Review the Assessment results")

12. You now have a list of the issues WWI will need to consider in upgrading their database to Azure SQL Database. Notice the assessment includes recommendations on the potential resolutions to issues. You can select **Export report** to save the report as a JSON file, if desired.

### Task 4: Migrate the database schema

After you have reviewed the assessment results and you have ensured the database is a candidate for migration to Azure SQL Database, use the Data Migration Assistant to migrate the schema to Azure SQL Database.

1. On the SqlServerDw VM, return to the Data Migration Assistant, and select the New **(+)** icon in the left-hand menu

2. In the New project dialog, enter the following:

    - **Project type**: Select Migration

    - **Project name**: Enter DwMigration

    - **Source server type**: SQL Server

    - **Target server type**: Azure SQL Database

    - **Migration scope**: Select Schema only

        ![The above information is entered in the New project dialog box.](media/data-migration-assistant-new-project-migration.png "New Project dialog")

    - Select **Create**

3. In the **Select source** tab, enter the following:

    - **Server name**: Enter SqlServerDw\SQLSERVER2008

    - **Authentication type**: Leave Windows Authentication selected

    - **Connection properties**: Check both Encrypt connection and Trust server certificate

    - Select **Connect**

    - Select **WorldWideImporters** from the list of databases

        ![The Select source tab of the Data Migration Assistant is displayed, with the values specified above entered into the appropriate fields.](media/data-migration-assistant-migration-select-source.png "Data Migration Assistant Select source")

4. Select **Next**

5. In the **Select target** tab, enter the following:

    - **Server name**: Enter the server name of the Azure SQL Database you created

        - To find the name of your SQL Database, select the WorldWideImporters SQL Database from your hands-on-lab-SUFFIX resource group in the Azure portal, and then select the **Server name** in the Essentials area of the Overview blade.

            ![On the SQL database Overview blade, the Server name is highlighted.](media/azure-sql-database-servername.png "SQL Database Overview")

    - **Authentication type**: Select SQL Server Authentication

    - **Username**: Enter demouser

    - **Password**: Enter Password.1!!

    - **Connection properties**: Check both Encrypt connection and Trust server certificate

    - Select **Connect**

    - Select **WorldWideImporters** from the list of databases

        ![The Select target tab of the Data Migration Assistant is displayed, with the values specified above entered into the appropriate fields.](media/data-migration-assistant-migration-select-target.png "Data Migration Assistant Select target")

6. Select **Next**

7. In the **Select objects** tab, leave all the objects checked, and select **Generate SQL script**

    ![The Select objects tab of the Data Migration Assistant is displayed, with all the objects checked.](media/data-migration-assistant-migration-select-objects.png "Data Migration Assistant Select target")

8. In the **Script & deploy schema** tab, review the script, then select **Deploy schema**

    ![The Script & deploy schema tab of the Data Migration Assistant is displayed, with the generated script shown.](media/data-migration-assistant-migration-script-and-deploy-schema.png "Data Migration Assistant Script & deploy schema")

9. Select **Deploy schema*

10. After the schema is deployed, review the deployment results, and ensure there were no errors

    ![The schema deployment results are displayed, with 226 commands executed and 0 errors highlighted.](media/data-migration-assistant-migration-deployment-results.png "Schema deployment results")

11. Next, open SSMS on the SqlServerDw VM, and connect to your Azure SQL Database, by selecting **Connect->Database Engine** in the Object Explorer, and then entering the server name and credentials into the Connect to Server dialog

    ![The SSMS Connect to Server dialog is displayed, with the Azure SQL Database name specified, SQL Server Authentication selected, and the demouser credentials entered.](media/ssms-connect-azure-sql-database.png "Connect to Server")

12. Once connected, expand **Databases**, and expand **WorldWideImporters**, then expand **Tables**, and observe the schema has been created

    ![In the SSMS Object Explorer, Databases, WorldWideImporters, and Tables are expanded, showing the tables created by the deploy schema script.](media/ssms-databases-worldwideimporters-tables.png "SSMS Object Explorer")

### Task 5: Create a migration project

In this task, you will create a new migration project for the WorldWideImporters database.

1. After the Azure Database Migration Service is created, navigate to it in the [Azure portal](https://portal.azure.com)

2. On the Azure Database Migration Service blade, select **+New Migration Project**

    ![On the Azure Database Migration Service blade, +New Migration Project is highlighted in the toolbar.](media/dms-add-new-migration-project.png "Azure Database Migration Service New Project")

3. On the New migration project blade, enter the following:

    - **Project name**: Enter OnPremToAzureSql

    - **Source server type**: Select SQL Server

    - **Target server type**: Select Azure SQL Database

        ![The New migration project blade is displayed, with the values specified above entered into the appropriate fields.](media/dms-new-migration-project-blade.png "New migration project")

4. Select **Create**

5. On the Migration Wizard **Select source** blade, enter the following:

    - **Source SQL Server instance name**: Enter the IP address of your SqlServerDw VM, plus the port number you used when creating the alias for your SQL Server 2008 R2 instance in SQL Server Configuration Manager. For example, `137.116.46.174,49700`.

        > **NOTE**: The format is **IP-Address,Port** with a comma between, not a colon (:) as is typically used when specifying a port number after an IP address.

    - **Authentication type**: Select SQL Authentication

    - **User Name**: Enter sa

    - **Password**: Enter Password.1!!

    - **Connection properties**: Check both Encrypt connection and Trust server certificate

        ![The Migration Wizard Select source blade is displayed, with the values specified above entered into the appropriate fields.](media/dms-migration-wizard-select-source.png "Migration Wizard Select source")

6. Select **Save**

7. On the Migration Wizard **Select databases** blade, select WorldWideImporters, and uncheck all databases

    ![The Migration Wizard Select databases blade is displayed, with the WorldWideImporters database selected, and all others unchecked.](media/dms-migration-wizard-select-databases.png "Migration Wizard Select databases")

8. Select **Save**

9. On the Migration Wizard **Select target** blade, enter the following:

    - Select **I know my target details**

    - **Target server name**: Enter the server name for your Azure SQL Database

        - To find the name of your SQL Database, select the WorldWideImporters SQL Database from your hands-on-lab-SUFFIX resource group in the Azure portal, and then select the **Server name** in the Essentials area of the Overview blade.

            ![On the SQL database Overview blade, the Server name is highlighted.](media/azure-sql-database-servername.png "SQL Database Overview")

    - **Authentication type**: Select SQL Authentication

    - **User Name**: Enter demouser

    - **Password**: Enter Password.1!!

    - **Connection properties**: Check Encrypt connection

        ![The Migration Wizard Select target blade is displayed, with the values specified above entered into the appropriate fields.](media/dms-migration-wizard-select-target.png "Migration Wizard Select target")

10. Select **Save**

11. On the Migration Wizard Summary blade, review the Project summary, then select **Save**

    ![The Migration Wizard summary blade is displayed.](media/dms-migration-wizard-summary.png "Migration Wizard summary")

12. On the Azure Database Migration Project blade, you will receive a success message, similar to the following

    ![On the Azure Database Migration Project blade, a success message is displayed.](media/dms-migration-project-successfully-created.png "DMS project created successfully")

### Task 6: Run the migration

In this task, you will create a new activity in the Azure Database Migration Service to execute the migration from the "on-premises" SQL Server 2008 R2 server to Azure SQL Database.

1. On the Azure Database Migration Service blade, select **+New Activity**, and then select **Run migration**

    ![On the Azure Database Migration Service blade, +New Activity is highlighted, and the Run migration button is highlighted in the Create new activity dialog.](media/dms-add-new-activity.png "Azure Database Migration Service Add New Activity")

2. On the Migration Wizard **Select source** blade, re-enter the sa password, **Password.1!!**, then select **Save**

    ![The Migration Wizard Select source blade is displayed, with the password value highlighted.](media/dms-migration-wizard-migration-source-detail.png "Migration Wizard Select source")

3. On the Migration Wizard **Select target** blade, re-enter the demouser password, **Password.1!!**, then select **Save**

    ![The Migration Wizard Select target blade is displayed, with the password value highlighted.](media/dms-migration-wizard-migration-target-detail.png "Migration Wizard Select target")

4. On the Migration Wizard **Map to target databases** blade, confirm that **WorldWideImporters** is checked as the source database, and that it is also the target database on the same line, then select **Save**

    ![The Migration Wizard Map to target database blade is displayed, with the WorldWideImporters line highlighted.](media/dms-migration-wizard-map-to-target-databases.png "Migration Wizard Map to target databases")

5. On the Migration Wizard **Configure migration settings** blade, expand the WorldWideImporters database, verify all the tables are selected, and select **Save**

    ![The Migration Wizard Configure migration settings blade is displayed, with the expand arrow for WorldWideImporters highlighted, and all the tables checked.](media/dms-migration-wizard-configure-migration-settings.png "Migration Wizard Configure migration settings")

6. On the Migration Wizard **Summary** blade, enter the following:

    - **Activity name**: Enter a name, such as Sql2008ToSqlDatabase

    - **Validation option**: Select Validate my database(s), check all three Validation options, and then select **Save**

        ![The Migration Wizard summary blade is displayed, Sql2008ToSqlDatabase is entered into the name field, and Validate my database(s) is selected in the Choose validation option blade, with all three validation options selected.](media/dms-migration-wizard-migration-summary.png "Migration Wizard Summary")

7. Select **Run migration**

8. Monitor the migration on the status screen that appears. Select the refresh icon in the toolbar to retrieve the latest status.

    ![On the Migration job blade, the status shows the job is Running.](media/dms-migration-wizard-status-running.png "Migration with Pending status")

9. When the migration is complete, you will see the status as **Completed**

    ![On the Migration job blade, the status of Completed is highlighted](media/dms-migration-wizard-status-complete.png "Migration with Completed status")

### Task 7: Verify data migration

In this task, you will use SSMS to verify the database was successfully migrated to Azure SQL Database.

1. Open SSMS on the SqlServerDw VM, and connect to your Azure SQL Database. In the Connect to Server dialog, enter the following:

    - **Server name**: Enter the server name of your Azure SQL Database

    - **Login**: Enter demouser

    - **Password:**: Enter Password.1!!

        ![The SSMS Connect to Server dialog is displayed, with the Azure SQL Database name specified, SQL Server Authentication selected, and the demouser credentials entered.](media/ssms-connect-azure-sql-database.png "Connect to Server")

2. Select **Connect**

3. In the Object Explorer, expand Databases, WorldWideImporters, and Tables, then right-click `dbo.DimCustomer`, and select **Select Top 1000 Rows**

    ![In SSMS, Databases, WorldWideImporters, and Tables are expanded, and the context menu for dbo.DimCustomer is displayed, with Select Top 1000 Rows highlighted in the menu.](media/ssms-select-top.png "Select Top 100 Rows")

4. Observe that the query returns results, showing the data has been migrated for the on-premises SQL Server 2008 R2 database into Azure SQL Database

## Exercise 3: Post upgrade enhancement

Duration: 30 minutes

In this exercise, you will confirm that the POC was successful. To demonstrate value from the upgrade, the Azure SQL Database features, Table Compression and ColumnStore Index, will be enabled to immediately show benefit.

### Task 1: Table compression

1. Open SSMS on the SqlServerDw VM, and connect to your Azure SQL Database

2. Open a new query window by selecting **New Query** from the toolbar

    ![The New Query icon is highlighted on the SSMS toolbar.](./media/ssms-toolbar-new-query.png "SSMS New Query")

3. Copy the script below, and paste it into the query window:

    ```sql
    USE [WorldWideImporters]

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

4. Select **Execute** on the toolbar to run the query to retrieve the size of the `FactInternetSales` table

    ![The Execute icon is highlighted on the SSMS toolbar.](./media/ssms-toolbar-execute-query.png "Select Execute")

5. In the results pane, note the size of the `FactInternetSales` table

    ![In the SSMS results pane, the size of the uncompressed FactInternetSales table is highlighted.](media/ssms-query-results-factinternetsales-uncompressed-size.png "Query results")

6. In the Object Explorer, expand Databases, WorldWideImporters, and Tables

    ![Databases is highlighted in Object Explorer, WorldWideImporters is highlighted below that, and then Tables is highlighted below that.](./media/ssms-object-explorer-tables.png "Select Tables")

7. Right-click the `FactInternetSales` table, select the **Storage** context menu, and then select **Manage Compression** from the fly-out menu

    ![The FactInternetSales table is selected on the left, Storage is selected and highlighted in the submenu, and Manage Compression is highlighted on the right.](./media/ssms-table-storage-context-menu.png "Select Manage Compression")

8. On the Welcome page of the Data Compression Wizard, select **Next**

9. On the Select Compression Type page, select **Row** from the Compression Type drop down, and select **Next**

    ![The Row compression type is highlighted on the Select Compression Type screen of the Data Compression Wizard.](media/ssms-data-compression-wizard-compression-type-row.png "Data Compression Wizard Select Compression Type")

10. On the Select an Output Option page, select **Run immediately**, and then select **Finish >>|**

    ![Run immediately is highlighted on the Select an Output Option screen of the Data Compression Wizard.](media/ssms-data-compression-wizard-output-row-compression.png "Data Compression Wizard Select an Output Option")

11. Select **Finish** on the Summary page

    ![The Summary screen of the Data Compression Wizard is displayed.](media/ssms-data-compression-wizard-summary-row-compression.png "Data Compression Wizard Summary")

12. Close the Data Compression Wizard, and rerun the query to get the size of the `FactInternetSales` table, noting the reduced size of the table

    ![In the SSMS results pane, the size of the row compressed FactInternetSales table is highlighted.](media/ssms-query-results-factinternetsales-row-compression-size.png "Query results")

13. Now, repeat steps 7 - 12 above, this time setting the Compression type to Page

    ![The Page compression type is highlighted on the Select Compression Type screen of the Data Compression Wizard](media/ssms-data-compression-wizard-compression-type-page.png "Data Compression Wizard Select Compression Type")

14. Once again, observe the table size in the results pane, and compare it to the values noted for the uncompressed table and with Row compression applied

    ![In the SSMS results pane, the size of the page compressed FactInternetSales table is highlighted.](media/ssms-query-results-factinternetsales-page-compression-size.png "Query results")

15. Both Row and Page compression reduce the size of the table, but page compression provides the greatest reduction in this case. Compression decreases the load on the Disk I/O subsystem, while increasing the load on the CPU. Since most data warehouse workloads are heavily disk bound, and often have low CPU usage, compression can be a great way to improve performance.

### Task 2: Clustered ColumnStore index

In this task, you will create a new table based on the existing `FactResellerSales` table and apply a ColumnStore index.

1. In SSMS, ensure you are connected to the Azure SQL Database instance

2. Open a new query window by selecting **New Query** from the toolbar

    ![The New Query icon is highlighted on the SSMS toolbar.](./media/ssms-toolbar-new-query.png "SSMS New Query")

3. Copy the script below, and paste it into the query window

    ```sql
    USE WorldWideImporters

    SELECT *
    INTO ColumnStore_FactResellerSales
    FROM FactResellerSales
    GO
    ```

4. Select **Execute** on the toolbar to run the query, and create a new table named `ColumnStore_FactResellerSales`, populated with data from the `FactResellerSales` table

    ![The Execute icon is highlighted on the SSMS toolbar.](./media/ssms-toolbar-execute-query.png "Select Execute")

5. Select **New Query** in the toolbar again, and paste the following query into the new query window. The query contains multiple parts; one to get the size of the `ColumnStore_FactResellerSales` table, a second to create a clustered ColumnStore index on the ColumnStore_FactResellerSales table, and then the size query is repeated to get the size after adding the clustered ColumnStore index.

    ```sql
    USE [WorldWideImporters]

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
    CREATE CLUSTERED COLUMNSTORE INDEX [cci_FactResllerSales]
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

6. Select **Execute** on the toolbar to run the query

7. In the query results, observe the `Size_MB` value of the table before and after the creation of the clustered ColumnStore index. The first value is the size before the index was created, and the second value is the size after the ColumnStore index was created.

    ![The SSMS results pane is displayed, with the size of the ColumnStore_FactResellerSales table highlighted both before and after the creation of the clustered ColumnStore index.](media/ssms-query-results-columnstore-factresellersales-size.png "ColumnStore_FactResellerSales size query results")

8. Create a new query window by selecting **New Query** from the toolbar, and select **Include Actual Execution Plan** by selecting its button in the toolbar

    ![The Include Actual Execution Plan icon is highlighted on the New Query the toolbar.](./media/ssms-toolbar-include-actual-execution-plan.png "Select the Include Actual Execution Plan")

9. Paste the queries below into the new query window, and select **Execute** on the toolbar

    ```sql
    SELECT productkey, salesamount
    FROM ColumnStore_FactResellerSales

    SELECT productkey, salesamount
    FROM FactResellerSales
    ```

10. In the Results pane, select the **Execution Plan** tab. Check the (relative to the batch) percentage value of the two queries and compare them.

    ![The Execution Plan tab is highlighted in the Results pane, 6% is highlighted for Query 1, and 94% is highlighted for Query 2.](./media/ssms-query-results-execution-plan-columnstore-index.png "Compare the two queries")

11. Run the same queries again, but this time set statistics IO on in the query by adding the following to the top of the query window

    ```sql
    SET STATISTICS IO ON
    GO
    ```

12. Your query should look like:

    ![The query includes the above information at the top.](./media/ssms-query-statistics-io.png "Set stastics IO")

13. Select **Execute** from the toolbar to run the query

14. Statistics IO reports on the amount of logical pages that are read in order to return the query results. Select the **Messages** tab of the Results pane, and compare two numbers, logical reads and lob logical reads. You should see a significant drop in total number of logical reads on the columns store table.

    ![Various information is highlighted on the Messages tab of the Results pane.](./media/ssms-query-results-messages-stastics-io.png "Compare the information")

## Exercise 4: Setup Oracle 11g Express Edition

Duration: 45 minutes

In this exercise, you will install Oracle XE on your Lab VM, load a sample database supporting an application, and then migrate the database to the "on-premises" SQL Server 2017 instance.

### Task 1: Install Oracle XE

1. Connect to your Lab VM, as you did in Task 4 of the [Before the Hands-on Lab](./Before%20the%20HOL%20-%20Data%20Platform%20upgrade%20and%20migration.md) exercise.

    - **User name**: demouser
    - **Password**: Password.1!!

2. In a web browser on your Lab VM, navigate to <http://www.oracle.com/technetwork/database/database-technologies/express-edition/downloads/index.html>

3. On the Oracle Database Express Edition 11g Release 2 page, accept the license agreement, and select **Oracle Database 11g Express Edition Release 2 for Windows x64**

    ![Accept the license agreement and Oracle Database 11g Express Edition Release 2 for Windows x64 are highlighted under Oracle Database Express Edition 11g Release 2.](./media/oracle-11g-download.png "Oracle 11g download")

4. Sign in with your Oracle account to complete the download. If you don't already have a free Oracle account, you will need to create one.

    ![This is a screenshot of the Sign in screen.](./media/oracle-sign-in.png "Sign in to complete the download")

5. After signing in, the file will download

6. Unzip the file, and navigate to the DISK1 folder

7. Right-click `setup.exe`, and select **Run as administrator**

    ![In File Explorer, setup.exe is selected, and Run as administrator is highlighted in the shortcut menu.](./media/windows-file-menu-run-as-administrator.png "Run setup.exe as an administrator")

8. Select **Next** to step through each screen of the installer, accepting the license agreement and default values, until you get to the **Specify Database Passwords** screen

9. On the **Specify Database Passwords** screen, set the password to **Password.1!!**, and select **Next**

    ![The above credentials are entered on the Specify Database Passwords screen.](./media/oracle-11g-install-passwords.png "Set the password")

10. On the Summary screen, take note of the ports being assigned, and select **Install**

    ![Several of the ports being assigned are highlighted on the Summary screen.](./media/oracle-11g-install-summary.png "Note the ports being assigned")

11. Select **Finish** on the final dialog to compete the installation

### Task 2: Install Oracle Data Access components

1. On your Lab VM, navigate to <http://www.oracle.com/technetwork/database/windows/downloads/index-090165.html>

2. Accept the license agreement, and select the **ODAC122010_x64.zip** download link under 64-bit ODAC 12.2c Release 1 (12.2.0.1.0) for Windows x64

    ![Accept the license agreement and ODAC122010\_x64.zip are highlighted on the 64-bit Oracle Data Access Components (ODAC) Downloads screen.](./media/oracle-odac-download.png "64-bit Oracle Data Access Components (ODAC) Downloads screen")

3. When the download completes, extract the contents of the ZIP file to a local drive

4. Navigate to the folder containing the extracted ZIP file, and right-click `setup.exe`, then select **Run as administrator** to begin the installation

5. Select **Next** to accept the default language, English, on the first screen

6. On the Specify Oracle Home User screen, accept the default, Use Windows Built-in Account, and select **Next**

7. Accept the default installation locations, and select **Next**

8. On the **Available Product Components**, uncheck **Oracle Data Access Components Documentation for Visual Studio**, and select Next

    ![Oracle Data Access Components Documentation for Visual Studio is cleared on the Available Product Components screen, and Next is selected at the bottom.](./media/oracle-odac-install-product-components.png "Clear Oracle Data Access Components Documentation for Visual Studio")

9. On the ODP.NET screen, check the box for **Configure ODP.NET and/or Oracle Providers for ASP.NET at machine-wide level**, and select **Next**

    ![Configure ODP.NET and/or Oracle Providers for ASP.NET at machine-wide level is selected on the ODP.NET screen, and Next is selected at the bottom.](./media/oracle-odac-install-odp-net.png "Select Configure ODP.NET and/or Oracle Providers for ASP.NET at machine-wide level")

10. On the DB Connection Configuration screen, enter the following:

    - **Connection Alias**: Northwind

    - **Port Number**: 1521

    - **Database Host Name**: localhost

    - **Database Service Name**: XE

        ![The information above is entered on the DB Connection Configuration screen, and Next is selected at the bottom.](./media/oracle-odac-install-db-connection.png "Enter the information")

    - Select **Next**

11. If the Next button is disabled on the Perform Prerequisite Checks screen, check the **Ignore All** box, and then select **Next**. This screen will be skipped by the installer if no missing requisites are found.

    ![The Ignore All box is cleared on highlighted on the Perform Prerequisite Checks screen, and Next is selected at the bottom.](./media/oracle-odac-install-prerequisite-checks.png "Perform Prerequisite Checks")

12. On the Summary screen, select **Install**

13. On the Finish screen, select **Close**

### Task 3: Install SQL Server Migration Assistant for Oracle

1. On your Lab VM, download SQL Server Migration Assistant v7.x for Oracle from <https://www.microsoft.com/en-us/download/details.aspx?id=54258>

2. Select the Download button to download SSMA

    ![Download is selected and highlighted under Microsoft SQL Server Migration Assistant v7.x for Oracle.](media/ssma-download.png "Download SSMA")

3. Check the box next to **SSMA for Oracle.7.x.0.msi**, and select **Next** to begin the download

    ![SSMAforOracle_7.x.0.msi is selected and highlighted under Choose the download you want.](media/ssma-download-files.png)

4. Run the downloaded installer, and select **Next** on the Welcome screen

    ![Next is selected on the SSMA for Oracle Welcome screen.](./media/ssma-installer-welcome.png " SSMA for Oracle Welcome screen")

5. Accept the License Agreement, and select **Next**

6. On the Choose Setup Type screen, select **Typical**, which will move you to the next screen

    ![Typical is selected and highlighted on the Choose Setup Type screen.](./media/ssma-install-setup-type.png "Select Typical")

7. Select **Install** on the Ready to Install screen

    ![Install is selected on the Ready to Install screen.](./media/ssma-install-ready-to-install.png "Select Install")

8. Select **Finish** when the installation is complete

### Task 4: Install dbForge Fusion tool

In this task, you will install a third-party extension to Visual Studio to enable interaction with, and script execution for, Oracle databases in Visual Studio 2017 Community Edition. This step is required because the Oracle Developer Tools extension does not currently work with Visual Studio 2017 Community Edition.

1. On your Lab VM, open a web browser and navigate to <https://www.devart.com/dbforge/oracle/fusion/download.html>

2. Scroll down on the page, and download a Trial of the current version by selecting the green download link

    ![Trial and Download are highlighted under dbForge Fusion, Current Version.](./media/dbforge-trial-download.png "dbForge Fusion, Current Version section")

3. Run the installer

    > Close Visual Studio if it is open to complete the installation.

4. Select **Next** on the Welcome screen

    ![Next is selected on the Devart dbForge Fusion for Oracle Welcome screen.](./media/dbforge-install-welcome.png "Select Next on the Welcome screen")

5. Select **Next** on each screen, accepting the license agreement and default settings, until reaching the Ready to Install screen

6. Select **Install** on the Ready to Install screen

    ![Install is selected on the Ready to Install screen.](./media/dbforge-install-ready-to-install.png "Select Install ")

7. Select **Finish** when the installation is complete

### Task 5: Create the Northwind database in Oracle 11g XE

WWI has provided you with a copy of their application, including a database script to create their Oracle database. They have asked that you use this as a starting point for migrating their database and application to SQL Server 2017. In this task, you will create a connection to the Oracle database on your Lab VM, and create a database called Northwind.

1. On your SqlServerDW VM, open SSMS, select **Connect** in the Object Explorer, and select **Database Engine**

    ![Database Engine is selected and highlighted under Connect, which is highlighted in Object Explorer.](./media/ssms-connect-database-engine.png "Select Database Engine")

2. In the Connect to Server dialog, select the SQL Server 2017 instance, **SqlServerDw**, from the Server name drop down, leave Authentication set to **Windows Authentication**, and select **Connect**

    ![SqlServerDw is selected and highlighted in the Server name drop-down list in the Connect to Server dialog box.](./media/ssms-connect-to-sqlserverdw.png "Select SqlServerDw")

3. In the Object Explorer, under the 2017 instance, SqlServerDw, right-click **Databases**, and select **New Database**

    ![Databases is highlighted under the SqlServerDw 2017 instance, and New Database is selected and highlighted in the submenu.](./media/ssms-databases-new-database.png "Select New Database")

4. In the New Database dialog, enter **Northwind** for the Database name, and select **OK**

    ![Northwind is highlighted in the Database name box in the New Database dialog box.](./media/ssms-databases-new-database-dialog.png "Enter the database name")

5. Now, on your LabVM, download the starter project by downloading a .zip copy of the Data Platform upgrade and migration GitHub repo

6. In a web browser, navigate to the [Data Platform upgrade and migration MCW repo](https://github.com/Microsoft/MCW-Data-Platform-upgrade-and-migration)

7. On the repo page, select **Clone or download**, then select **Download ZIP**

    ![Download .zip containing the Data Platform upgrade and migration repository](media/git-hub-download-repo.png "Download ZIP")

8. Unzip the contents to **C:\handsonlab**

9. Within the **handsonlab** folder, navigate to the **starter-project** folder under `Hands-on lab`, and double-click `NorthwindMVC.sln` to open the project in Visual Studio 2017

10. If prompted for how you want to open the file, select **Visual Studio 2017**, and select **OK**

    ![Visual Studio 2017 is selected and highlighted under How do you want to open this file?](./media/visual-studio-version-selector.png "Open Visual Studio 2017")

11. Sign into Visual Studio (or create an account if you don't have one), when prompted

12. At the Security Warning screen, uncheck **Ask me for every project in this solution**, and select **OK**

    ![Ask me for every project in this solution is cleared and OK is selected on the Security Warning screen.](./media/visual-studio-security-warning.png "Clear Ask me for every project in this solution")

13. Once then solution is open in Visual Studio, select the **Fusion** menu, and select **New Connection**

    ![New Connection is highlighted in the Fusion menu in Visual Studio.](./media/visual-studio-fusion-menu-new-connection.png "Select New Connection")

14. In the Database Connection properties dialog, set the following values:

    - **Host**: localhost

    - **Port**: Leave 1521 selected

    - Select **SID**, and enter **XE**

    - **User**: system

    - **Password**: Password.1!!

    - Check **Allow saving password**

    - **Connect as**: Normal

    - **Connection Name**: Northwind

        ![The information above is entered in the Database Connection Properties * Oracle dialog box, and OK is selected at the bottom.](./media/visual-studio-fusion-new-database-connection.png "Specify the settings")

15. Select **Test Connection** to verify the settings are correct, and select **OK** to close the popup

16. Select **OK** to create the Database Connection

17. You will now see the Northwind connection in the Database Explorer window

    ![The Northwind connection is selected in the Database Explorer window.](./media/visual-studio-fusion-database-explorer.png "View the Northwind connection")

18. Select **Fusion** again from the Visual Studio menu, and then select **New Query**. Note: You may receive a notification that your trial has expired when you do this. This can be ignored for this hands-on lab. Close that dialog, and continue to the query window that opens in Visual Studio.

    ![New Query is highlighted in the Fusion menu in Visual Studio.](./media/visual-studio-fusion-menu-new-query.png "Select New Query")

19. You will be working in the Text area of the query window, so at the bottom of the query window, select the **Swap views** icon to move the Text area to the top of the window, and the Query Builder to the bottom. You can then use the mouse to expand the Text area. Note: You may need to select **Text** before it allows you to swap the positions of the two windows.

    ![The Swap views icon is highlighted at the bottom of the query window. ](./media/visual-studio-fusion-swap-view.png "Select the Swap views icon")

20. In Visual Studio, select **File** in the menu, then select **Open File**, and navigate to `C:\handsonlab\MCW-Data-Platform-upgrade-and-migration-master\Hands-on lab\starter-project\Oracle Scripts`, select the file `1.northwind.oracle.schema.sql`, and then select **Open**.

    ![The file, 1.northwind.oracle.schema.sql, is selected and highlighted in the Open File window.](./media/visual-studio-open-file.png "Open File dialog")

21. Select and copy all the file contents (use CTRL+A, CTRL+C)

22. Select the tab for your Fusion query (Query1.sql)

    ![Query1.sql is selected and highlighted on the tab for your Fusion query.](./media/visual-studio-fusion-query.png "Fusion query window")

23. Paste (CTRL+V) the text copied in the previous step into the Text area of the query window

    ![The text from the previous step is pasted and highlighted in the text area at the bottom of the query window.](./media/visual-studio-fusion-query-window.png "Paste the text")

24. Select the **Execute** Fusion script button on the Visual Studio toolbar

    ![The Execute Fusion script icon is highlighted on the Visual Studio toolbar.](./media/visual-studio-fusion-execute.png "Run the script")

25. The results of execution can be viewed in the Output window, found at the bottom left of the Visual Studio window

    ![Output is highlighted in the Output window.](./media/visual-studio-fusion-output-query-1.png "View the results")

26. In the Database Explorer window, right-click on the **Northwind** connection, and select **Modify Connection** (If the Database Explorer is not already open, you can open it by selecting Fusion in the menu, then selecting Database Explorer)

    ![Modify Connection is highlighted in the submenu for the Northwind connection in the Database Explorer window.](./media/visual-studio-database-explorer-modify-connection.png "Modify Connection")

27. In the Modify Connection dialog, change the username and password as follows:

    - **User name**: NW

    - **Password**: oracledemo123

28. Select **Test Connection** to verify the new credentials work

    ![The information above is entered and highlighted in the Database Connection Properties * Oracle dialog box, and Test Connection is selected at the bottom.](./media/visual-studio-database-explorer-modify-connection-update.png "Specify the settings")

29. Select **OK** to close the Database Connection properties dialog

30. Return to your Fusion query window in Visual Studio

31. Delete all the query text pasted into the Text area previously. (Click in the Text area, press CTRL+A, then press Delete)

32. Select the **Open File** icon on the Visual Studio toolbar

    ![The Open File icon is highlighted on the Visual Studio toolbar.](./media/visual-studio-toolbar-open-file.png "Select Open File")

33. In the Open File dialog, navigate to `C:\handsonlab\MCW-Data-Platform-upgrade-and-migration-master\Hands-on lab\starter-project\Oracle Scripts`, select the file `2.northwind.oracle.tables.views.sql`, and then select **Open**

34. As you did previously, copy all the text from the file, and paste it into the Text area of the Fusion query window

35. Select the **Execute** Fusion script button on the toolbar, and view the results of execute in the Output pane

    ![The Execute Fusion script icon is highlighted on the Visual Studio toolbar.](./media/visual-studio-fusion-execute.png "Run the script")

36. Repeat steps 29 - 31, replacing the file name in step 29 with each of the following:

    - `3.northwind.oracle.packages.sql`

    - `4.northwind.oracle.sps.sql`

        - During the Execute script step for this file, you will need to execute each CREATE OR REPLACE statement independently

        - Using your mouse, select the first statement, starting with CREATE and going to END;

        ![The first statement between CREATE and END is highlighted.](./media/visual-studio-fusion-query-selection.png "Select the first statement")

        - Next, select Execute Selection in the Visual Studio toolbar

        ![Execute Selection is highlighted on the Visual Studio toolbar.](./media/visual-studio-fusion-query-execute-selection.png "Select Execute Selection")

        - Repeat this for each of the remaining CREATE OR REPLACE... END; blocks in the script file (there are 7 more to execute, for 8 total)

    - `5.northwind.oracle.seed.sql`

        - This query can take several minutes to run, so make sure you wait until you see **Execute succeeded*- in the output window before executing the next file, like the following:

        ![This is a screenshot of the Execute succeeded message in the output window.](./media/visual-studio-fusion-query-completed.png "Execute succeeded message")

    - `6.northwind.oracle.constraints.sql`

### Task 6: Configure the Starter Application to use Oracle

In this task, you will add the necessary configuration to the NorthwindMVC solution to connect to the Oracle database you created in the previous task.

1. In Visual Studio on your LabVM, select **Build** from the menu, then select **Build Solution**

    ![Build Solution is highlighted in the Build menu in Visual Studio.](./media/visual-studio-menu-build-build-solution.png "Select Build Solution")

2. Open `Web.config` by double-clicking the file in the Solution Explorer, on the right-hand side in Visual Studio

    ![Web.config is selected under Solution 'NorthwindMVC' (1 project) in Solution Explorer.](./media/visual-studio-solution-explorer-northwindmvc-web-config.png "Open Web.config")

3. In the `Web.config` file, locate the `connectionStrings` section, and verify the connection string named **OracleConnectionString** matches the values you have used in this hands-on lab:

    ```csharp
    DATA SOURCE=localhost:1521/XE;PASSWORD=oracledemo123;USER ID=NW
    ```

    ![The information above is highlighted in the Web.config file.](./media/visual-studio-web-config-connection-strings.png "Verify the connection string")

4. Run the solution by selecting the green **Start** button on the Visual Studio toolbar

    ![Start is selected on the toolbar.](./media/visual-studio-toolbar-start.png "Run the solution")

5. You should see the Northwind Traders Dashboard load in your browser

    ![The Northwind Traders Dashboard is visible in a browser.](./media/northwind-traders-dashboard.png "View the dashboard")

6. Close the browser to stop debugging the application, and return to Visual Studio

## Exercise 5: Migrate the Oracle database to SQL Server 2017

Duration: 30 minutes

In this exercise, you will migrate the Oracle database into the "on-premises" SQL Server 2017 instance using SSMA.

### Task 1: Migrate the Oracle database to SQL Server 2017 using SSMA

1. On your LabVM, launch **Microsoft SQL Server Migration Assistant for Oracle** from the Start Menu

2. Select **File**, then **New Project...**

    ![File and New Project are highlighted in the SQL Server Migration Assistant for Oracle.](./media/ssma-menu-file-new-project.png "Select New Project")

3. In the New Project dialog, accept the default name and location, select **SQL Server 2017** for the Migration To value, and select **OK**

    ![In the New Project dialog box, SQL Server 2017 is selected and highlighted in the Migration To box.](./media/ssma-new-project.png "New Project dialog box")

4. Select **Connect to Oracle** in the SSMA toolbar

    ![Connect to Oracle is highlighted on the SSMA toolbar.](./media/ssma-toolbar-connect-to-oracle.png "Select Connect to Oracle")

5. In the Connect to Oracle dialog, enter the following:

    - **Provider**: Leave set to the default value, Oracle Client Provider

    - **Mode**: Leave set to Standard mode

    - **Server name**: Enter localhost

    - **Server port**: Set to 1521

    - **Oracle SID**: Enter XE

    - **User name**: Enter NW

    - **Password**: Enter oracledemo123

        ![The information above is entered in the Connect to Oracle dialog box, and Connect is selected at the bottom.](./media/ssma-connect-to-oracle.png "Specify the settings")

6. Select **Connect**

7. In the Output window, you will see a message that the connection was established successfully, similar to the following:

    ![The successful connection message is highlighted in the Output window.](./media/ssma-connect-to-oracle-success.png "View the successful connection message")

8. Under Oracle Metadata Explorer, expand the localhost node, Schemas, and confirm you can see the NW schema, which will be the source for the migration

    ![The NW schema is highlighted in Oracle Metadata Explorer.](./media/ssma-oracle-metadata-explorer-nw.png "Confirm the NW schema")

9. Next, select **Connect to SQL Server** from the toolbar, to add your SQL 2017 connection

    ![Connect to SQL Server is highlighted on the toolbar.](./media/ssma-toolbar-connect-to-sql-server.png "Select Connect to SQL Server")

10. In the connect to SQL Server dialog, provide the following:

    - **Server name**: Enter the IP address of your SqlServerDw VM. You can get this from the Azure portal by navigating to your VM's blade, and looking at the Essentials area.

        ![The IP address of your SqlServerDw VM is highlighted in the Essentials area of your VM's blade in the Azure portal.](./media/azure-sql-database-public-ip-address.png "Enter the IP address ")

    - **Server port**: Leave set to [default]

    - **Database**: Enter Northwind

    - **Authentication**: Set to SQL Server Authentication

    - **User name**: Enter sa

    - **Password**: Enter Password.1!!

        ![The information above is entered in the Connect to SQL Server dialog box, and Connect is selected at the bottom.](./media/ssma-connect-to-sql-server.png "Specify the settings")

11. Select **Connect**.
12. You will see a success message in the output window

    ![The successful connection message is highlighted in the Output window.](./media/ssma-connect-to-sql-server-success.png "View the successful connection message")

13. In the SQL Server Metadata Explorer, expand your servers node, then Databases. You should see Northwind listed.

    ![Northwind is highlighted under Databases in SQL Server Metadata Explorer.](./media/ssma-sql-server-metadata-explorer-northwind.png "Verify the Northwind listing")

14. In the Oracle Metadata Explorer, check the box next to NW, and make sure it is selected in the tree

    ![The NW schema is selected and highlighted in Oracle Metadata Explorer.](./media/ssma-oracle-metadata-explorer-nw-selected.png "Confirm the NW schema")

15. In the SQL Server Metadata explorer, check the box next to Northwind

    ![Northwind is selected and highlighted under Databases in SQL Server Metadata Explorer.](./media/ssma-sql-server-metadata-explorer-northwind-selected.png "Select Northwind")

16. In the SSMA toolbar, select **Convert Schema**. There is a bug in SSMA which prevents this button to being properly enabled, so if the button is disabled, you can expand the NW node in the Oracle Metadata Explorer, which should cause the Convert Schema button to become enabled. You can also right-click on the NW database in the Oracle Metadata Explorer, and select Convert Schema if that does not work.

    ![Convert Schema is highlighted on the SSMA toolbar.](./media/ssma-toolbar-convert-schema.png "Select Convert Schema")

17. After about a minute the conversion should have completed

18. In the SQL Server Metadata Explorer, observe that new schema objects have been added. For example, under Northwind, Schemas, NW, Tables you should see the tables from the Oracle database

    ![NW is selected in the SQL Server Metadata Explorer, and tables from the Oracle database are visible below that.](./media/ssma-sql-server-metadata-explorer-nw-tables.png "Observe new schema objects")

19. In the output pane, you will notice a message that the conversion finished with 1 error, and 22 warnings

    ![The conversion message is highlighted in the Output window.](./media/ssma-convert-schema-output.png "View the conversion message")

20. To view the errors, select the **Error List** at the bottom of the SSMA screen

    ![Errors List is highlighted at the bottom of the SSMA screen.](./media/ssma-error-list.png "Select Errors List")

21. Select **Warnings** to hide the warnings, and leave only Errors displayed

    ![The Warnings notification is highlighted in the Errors List.](./media/ssma-warnings.png "Warning tab")

22. Double-click on the error listed. This will display the Table in both Oracle and SQL Server that is causing the error, `EMPLOYEETERRITORIES`. Notice the Oracle table lists `EMPLOYEEID` with a data type of `NUMBER`, while SQL Server is expecting a data type of `float(53)`.

    ![NUMBER and float(53) are highlighted under Data Type, and EMPLOYEEID is selected in the split-screen views of the Table tab for Oracle and SQL Server.](./media/ssma-data-type-error.png "View the information")

23. Look at the table definition for the table on the Oracle side

24. To change the data type, select the **Type Mapping** tab, select the row with source type of number, and select **Edit**

    ![The Type Mapping tab is highlighted, and below that, the row with source type of number is highlighted, and Edit is highlighted on the right.](./media/ssma-source-type-mapping-number-edit.png "Change the data type")

25. In the Edit Type Mapping dialog, set the Target type to **numeric(precision, scale)**, set the Precision to **10**, and select **OK**

    ![The information above is entered in the Edit Type Mapping dialog box, and OK is selected at the bottom.](./media/ssma-source-type-mapping-edit-type-mapping-number.png "Specify the settings")

26. Select **Apply** on the Type mapping tab

    ![The row with source type of number is highlighted on the Type Mapping tab, and Apply is highlighted at the bottom.](./media/ssma-source-type-mapping-number-edit-apply.png "Select Apply")

27. In the Oracle Metadata Explorer, right-click the `EMPLOYEETERRITORIES` table, and select **Convert Schema**

    ![Convert Schema is highlighted in the submenu of the EMPLOYEETERRITORIES table in Oracle Metadata Explorer.](./media/ssma-table-convert-schema.png "Select Convert Schema")

28. When prompted that the target exists, select **Overwrite**

    ![Overwrite is selected and highlighted in the Target Exists prompt.](./media/ssma-table-convert-schema-overwrite.png "Select Overwrite")

29. Notice the error is now gone from the Error List

30. We are going to fix another data type conversion issue now, which will otherwise appear when we attempt to migrate the data

31. Select the `ORDER_DETAILS` table in the Oracle Metadata Explorer

    ![The ORDER_DETAILS table is selected and highlighted in Oracle Metadata Explorer.](./media/ssma-oracle-metadata-explorer-order-details.png "Select the ORDER_DETAILS table ")

32. Next, you are going to convert the type associated with the `DISCOUNT` column, `FLOAT(23)` to a `numeric(10, 0)`, similar to what you did for the `EMPLOYEETERRITORIES` table

33. Select the **Type Mapping** tab, then select `float[*..53]` from the Source Type list, and select **Edit**

    ![The row with source type of float\[\*..53\] is selected and highlighted on the Type Mapping tab, and Edit is highlighted on the right.](./media/ssma-type-mapping-float-edit.png "Edit float[*..53]")

34. In the Edit Type Mapping dialog, change the Target type to `numeric(precision, scale)`, and set the Precision to **10**, then select **OK**

    ![In the Edit Type Mapping dialog box, numeric(precision, scale) is highlighted under Target type, 10 is highlighted next to Replace with under Precision, and OK is highlighted at the bottom.](./media/ssma-edit-type-mapping-float-to-numeric.png "Change the Target type")

35. Select Apply to save the changes to the `ORDER_DETAILS` table

36. Now, right-click on the `ORDER_DETAILS` table in the Oracle Metadata Explorer, and select **Convert Schema**

37. When prompted that the target exists, select **Overwrite**

38. [Optional] Save the project. This can take a while, and is not necessary to complete the hands-on lab

39. To apply the resultant schema to the Northwind database in SQL Server, use the SQL Server Metadata Explorer to view the Northwind database. Right-click Northwind, and select **Synchronize with Database**.

    ![Synchronize with Database is highlighted in the submenu of the Northwind database in SQL Server Metadata Explorer.](./media/ssma-synchronize-with-database.png "Select Synchronize with Database")

40. Select **OK** in the Synchronize with the Database dialog

41. The Synchronize action will result in multiple errors in the Error List, resulting from attempting to add the SSMA assemblies to the Northwind database

    ![The Errors notification is highlighted at the top of the Errors List window, and the first of multiple errors is selected and highlighted in the Message list.](./media/ssma-synchronize-errors.png "View the errors")

42. These errors are the result of improvements implemented in SQL Server 2017 SQLCLR security model. Specifically, in SQL Server 2017, Microsoft now by default requires that all type of assemblies (SAFE, EXTERNAL_ACCESS, UNSAFE) are authorized for UNSAFE access.

43. For this hands-on lab, you will be adding the assemblies causing the errors to the trusted assembly list, which is synonymous with whitelisting the assemblies. To fix these errors, complete the following:

    - Under the **Northwind** database in the SQL Server Metadata Explorer in SSMA, expand **Assemblies**

        ![Three items are listed below Assemblies, which is highlighted below the Northwind database in SQL Server Metadata Explorer.](./media/ssma-sql-server-metadata-explorer-northwind-assemblies.png "Expand Assemblies")

    - Right-click `SSMA4OracleSQLServerCollections.NET`, and select **Save as Script**.

        ![Save as Script is highlighted in the submenu for SSMA4OracleSQLServerCollections.NET.](./media/ssma-sql-server-metadata-explorer-northwind-assemblies-save-as-script.png "Select Save as Script")

    - Save the script to the local machine

    - Now, you will need to use SSMS on your SqlServerDw VM

        - Open an RDP connection to your SqlServerDw VM, if one is not already open

        - Open SSMS 17

        - Connect to the SQL Server 2017 instance (SqlServerDw), by entering **SqlServerDw** into the Server name field, using Windows Authentication, and selecting **Connect**

        - Expand **Databases**, right-click on **Northwind**, and select **New Query**

        - Paste the following query into the new query window, but don't execute it until you complete the steps below

            ```sql
            USE master;
            GO

            DECLARE @clrName nvarchar(4000) = 'SSMA4OracleSQLServerCollections.NET'
            DECLARE @asmBin varbinary(max) = [INSERT BINARY];
            DECLARE @hash varbinary(64);

            SELECT @hash = HASHBYTES('SHA2_512', @asmBin);

            EXEC sys.sp_add_trusted_assembly @hash = @hash, @description = @clrName;
            ```

    - Now, return to your Lab VM, and open the saved `SSMA4OracleSQLServerCollections.NET.sql` file from the desktop with Notepad.exe

    - Within the SQL file, locate the line that begins with `CREATE ASSEMBLY`, then locate the word `FROM`. Copy the binary string that appears after `FROM`. This value will span all the way down to the line containing the text `WITH PERMISSION_SET = SAFE`. Be sure not to include any whitespace at the end of the binary value.

    ![The binary string that appears after FROM is highlighted within the SQL file.](./media/assembly-binary-value.png "Copy the binary string")

    - Now, return to SSMS on your SqlServerDw VM, and replace `INSERT BINARY` with the copied binary value. The line should end with ";" and there should be no whitespace before the ";"

    - Execute the query in SSMS

44. Repeat step 34, this time for the assembly `SSMA4OracleSQLServerExtensions.NET`. Make sure to replace the `@clrName` variable in the script with the value "SSMA4OracleSQLServerExtensions.NET"

45. The SSMA assemblies have now been whitelisted in SQL Server 2017

46. Return to SSMA on your Lab VM, and rerun the **Synchronize with Database** action on the Northwind database. This will create all the schema objects in the SQL Server Northwind database. There should now be no errors, and the Output pane should show **Synchronization operation is complete**.

47. Now you need to migrate the data

48. In the Oracle Metadata Explorer, select **NW** and from the command bar, select **Migrate Data**

    ![Migrate Data is highlighted in the command bar of Oracle Metadata Explorer.](./media/ssma-toolbar-migrate-data.png "Select Migrate Data")

49. You will be prompted to re-enter your Oracle credentials for use by the migration connection

    - Recall the Oracle credentials are:

        - **Server name**: localhost

        - **Server port**: 1521

        - **Oracle SID**: XE

        - **User name**: NW

        - **Password**: oracledemo123

    - The SQL Server credentials are:

        - **Server name**: IP address of your SqlServerDw VM (obtained in the essentials area of your VM's blade in Azure portal)

        - **Server port**: [default]

        - **Authentication**: SQL Server Authentication

        - **User name**: sa

        - **Password**: Password.1!!

50. Select **Connect**

51. After the migration completes, you will be presented with a Data Migration Report, similar to the following:

    ![This is screenshot of an example Data Migration Report.](./media/ssma-data-migration-report.png "View the Data Migration Report")

52. Select **Close** on the migration report

53. Close SSMA for Oracle

## Exercise 6: Migrate the Application

Duration: 15 minutes

In this exercise, you will modify the NorthwindMVC application, so it targets SQL Server 2017 instead of Oracle.

### Task 1: Create a new Entity Model against SQL Server

1. On your Lab VM, return to Visual Studio, and open `Web.config` from the Solution Explorer

2. Modify the connection string named `SqlServerConnectionString` to match your remote SQL Server credentials

    - Replace the value of "data source" with your SqlServerDw VM's public IP address

    - Replace the value of "password" with Password.1!!

    ![The information above is highlighted in Web.config.](./media/visual-studio-web-config-connection-string-sql-server.png "Replace the password value")

3. Build the solution, by selecting Build in the Visual Studio menu, then selecting Build Solution

4. In the Solution Explorer, expand the Data folder, and select all the files within the folder

    ![In Solution Explorer, all the files under Data (highlighted) are selected.](./media/visual-studio-solution-explorer-data-folder.png "Expand the Data folder")

5. Right-click, and choose **Delete**

    ![Delete is selected in the shortcut menu for all the files listed under Data.](./media/visual-studio-solution-explorer-data-folder-delete.png "Delete the files")

6. Select **OK** at the confirmation prompt

7. Right-click on the Data folder, and select **Add > New Item...**

    ![In the shortcut menu for the Data folder, New Item and Add are highlighted.](./media/visual-studio-solution-explorer-data-add-new-item.png "Select Add")

8. In the Add New Item dialog, expand Visual C#, select **Data**, and select **ADO.NET Entity Data Model**. Enter **DataContext** for the name, and select **Add**.

    ![In the Add New Item dialog box, Visual C#, Data, ADO.NET Entity Data Model, and DataContext are highlighted.](./media/visual-studio-solution-explorer-data-add-new-data-context.png "Add DataContext")

9. In the wizard's Choose Model Contents dialog, select **Code First from database**, and select **Next**

    ![Code First from database is highlighted under What should the model contain? in the Entity Data Model Wizard.](./media/visual-studio-entity-data-model-wizard-choose-model.png "Select Code First from database")

10. In the Choose Your Data Connection dialog, select **SqlServerConnectionString (Settings)** from the drop down, and select **Yes, include the sensitive data in the connection string**. **Uncheck Save connection settings in Web.Config**, and select **Next**.

    ![SqlServerConnectionString (Settings) and Yes, include the sensitive data in the connection string are selected and highlighted in the Entity Data Model Wizard, and Save connection settings in Web.Config is cleared.](./media/visual-studio-entity-data-model-wizard-data-connection.png "Choose Your Data Connection settings")

11. If prompted, in the Connect to SQL Server dialog, enter the Password, **Password.1!!**

    ![The password above is entered in the Connect to SQL Server dialog box.](./media/visual-studio-entity-data-model-wizard-connect-to-sql-server.png "Enter the Password")

12. On the Choose Your Database Objects and Settings screen, expand the Tables node, and check **NW** only. Ensure **Pluralize or singularize generated column names** is checked.

    ![The Tables node is selected, and NW is selected and highlighted in the Entity Data Model Wizard. Pluralize or singularize generated column names is also selected.](./media/visual-studio-entity-data-model-wizard-database-objects.png "Choose Your Database Objects and Settings")

13. Select **Finish**, and the model will be generated. This may take a few minutes

### Task 2: Modify Application Code

1. In Visual Studio, open the file `DataContext.cs` from the Solution Explorer. You may need to collapse the Data folder, and re-expand it after refreshing if you don't see the file listed

    ![DataContext.cs is highlighted under the Data folder in Solution Explorer.](./media/visual-studio-solution-explorer-data-datacontext.png "Open DataContext.cs")

2. The call to base in the DataContext constructor, at the top of the file, needs to be updated to reflect the correct connection string

    ![In the DataContext constructor, : base ("name=DataContext") is highlighted.](./media/visual-studio-solution-explorer-data-datacontext-base.png "Update the call to base")

3. Change the line from:

    ```csharp
    : base ("name=DataContext")
    ```

4. To:

    ```csharp
    : base ("name=SqlServerConnectionString")
    ```

5. Save the file

    ![In the DataContext constructor, : base ("name=SqlServerConnectionString") is highlighted.](./media/visual-studio-solution-explorer-data-datacontext-base-updated.png "Update the call to base")

6. Next, open the file `HomeController.cs`, in the Controllers folder in the Solution Explorer

    ![The HomeController.cs file is selected and highlighted under the Controllers folder in Solution Explorer.](./media/visual-studio-solution-explorer-controllers-home-controller.png "Open HomeController.cs")

7. Comment out the code under the Oracle comment. First, select the lines for the Oracle code, then select the Comment button in the toolbar.

    ![The code under the Oracle comment is highlighted and labeled 1, and the Comment button in the toolbar is highlighted and labeled 2.](./media/visual-studio-home-controller-comment-out-oracle-lines.png "Comment out code")

8. Next, uncomment the code under the SQL Server comment. Select the commented out code, then select the Uncomment button on the toolbar. You may need to click the Uncomment button twice to uncomment the code.

    > The lines will change from green to colored text when the comment characters have been removed from the front of each line. This code change is done because of differences in how stored procedures are accessed in Oracle versus Sql Server.

    ![The code under the SQL Server comment is highlighted and labeled 1, and the Uncomment button in the toolbar is highlighted and labeled 2.](./media/visual-studio-home-controller-uncomment-sql-server-lines.png "Uncomment code")

9. Save the changes to `HomeController.cs`

10. Open the file, `SALESBYYEAR.cs`, in the Models folder in the Solution Explorer

    ![SALESBYYEAR.cs is highlighted under the Models folder in the Solution Explorer.](./media/visual-studio-models-salesbyyear.png "Open SALESBYYEAR.cs")

11. Change the types of the following properties:

    - Change the `SUBTOTAL` property from double to decimal

    - Change the `YEAR` property from string to int

    ![The decimal and int property values are highlighted.](./media/visual-studio-models-salesbyyear-updated.png "Change the SUBTOTAL and YEAR properties")

12. Save the file

13. Open the `SalesByYearViewModel.cs` file from the Models folder in the Solution Explorer

    ![SalesByYearViewModel.cs is highlighted under the Models folder in the Solution Explorer.](./media/visual-studio-models-salesbyyearviewmodel.png "Open SalesByYearViewModel.cs")

14. Change the type of the `YEAR` property from string to int, then save the file

    ![The int property value is highlighted.](./media/visual-studio-models-salesbyyearviewmodel-updated.png "Change the YEAR property")

15. Run the solution by selecting the green Start button on the toolbar

    ![Start is highlighted on the toolbar.](./media/visual-studio-toolbar-start.png "Select Start")

16. You will get an exception that the stored procedure call has failed. This is because of an error in migrating the stored procedure.

    ![An exception appears indicating that the stored procedure call has failed.](./media/visual-studio-exception-sqlexception.png "View the error")

17. Select the red Stop button to end execution of the application

    ![The Stop button is highlighted on the toolbar.](./media/visual-studio-toolbar-stop.png "Select Stop")

18. To resolve the error, open the `SALES_BY_YEAR_fix.sql` file, located under Solution Items in the Solution Explorer

19. Note the database name at the top of the file (within the brackets after the `USE` keyword)

    ![NorthwindMigration is displayed as the database name at the top of the file.](./media/visual-studio-sql-file-use-statement-northwindmigration.png "Note the database name")

20. Replace this with the correct database name, **Northwind**, and save the file

    ![Northwind is listed as the correct database name at the top of the file.](./media/visual-studio-sql-file-use-statement-northwind.png "Enter the correct database name")

21. From the Visual Studio menu, select **View**, and then **Server Explorer**

    ![View and Server Explorer are highlighted in the Visual Studio menu.](./media/visual-studio-menu-view-server-explorer.png "Select Server Explorer")

22. In the Server Explorer, right-click on **Data Connections**, and select **Add Connections...**

    ![Data Connections is selected in Server Explorer, and Add Connection is highlighted in the shortcut menu.](./media/visual-studio-server-explorer-data-connections.png "Select Add Connection")

23. On the Choose Data Source dialog, select **Microsoft SQL Server**, and select **Next**

    ![Microsoft SQL Server is selected and highlighted under Data source in the Choose Data Source dialog box.](./media/visual-studio-server-explorer-data-connections-add.png "Select Microsoft SQL Server")

24. On the Add Connection dialog, enter the following:

    - **Data source**: Leave Microsoft SQL Server (SqlClient)

    - **Server name**: Enter the IP address of your SqlServerDw VM

    - **Authentication**: Select SQL Server Authentication

    - **User name**: Enter sa

    - **Password**: Enter Password.1!!

    - **Connect to a database**: Choose Select or enter database name, and enter Northwind

    - Select **Test Connection** to verify your settings are correct, and select **OK** to close the successful connection dialog

        ![The information above is entered in the Add Connection dialog box, and Test Connection is selected at the bottom.](./media/visual-studio-server-explorer-data-connections-add-connection.png "Specify the settings")

25. Select **OK**

26. Right-click the newly added SQL Server connection in the Server Explorer, and select **New Query**

    ![The newly added SQL Server connection is selected in Server Explorer, and New Query is highlighted in the shortcut menu.](./media/visual-studio-server-explorer-data-connections-new-query.png "Select New Query")

27. Select and copy all of the text from the `SALES_BY_YEAR_fix.sql` file (click CTRL+A, CTRL+C in the `SALES_BY_YEAR_fix.sql` file)

28. Paste (CTRL+V) the copied text into the new Query window

29. Verify `Use [Northwind]` is the first line of the file, and that it matches the database listed in the query bar, then select the green **Execute** button

    ![The Use [Northwind] statement is highlighted, as is the Northwind database and the Execute button in the query bar.](./media/visual-studio-sql-query-execute.png "Verify the Use [Northwind] statement")

30. You should see a message that the command completed successfully

    ![This is a screenshot of a message that the command completed successfully.](./media/visual-studio-sql-query-completed-successfully.png "View the message")

31. Run the application again by clicking the green Start button in the Visual Studio toolbar

    ![The Start button is highlighted on the Visual Studio toolbar.](./media/visual-studio-toolbar-start.png "Select Start")

32. Verify the graph is showing correctly on the Northwind traders dashboard

    ![The Northwind Traders Dashboard is visible in a browser.](./media/northwind-traders-dashboard.png "View the dashboard")

33. Congratulations! You have successfully migrated the data and application from Oracle to SQL Server

## After the hands-on lab

Duration: 10 mins

In this exercise, you will delete any Azure resources that were created in support of the lab. You should follow all steps provided after attending the Hands-on lab to ensure your account does not continue to be charged for lab resources.

### Task 1: Delete the resource group

1. Using the [Azure portal](https://portal.azure.com), navigate to the Resource group you used throughout this hands-on lab by selecting Resource groups in the left menu

2. Search for the name of your research group, and select it from the list

3. Select Delete in the command bar, and confirm the deletion by re-typing the Resource group name, and selecting Delete

You should follow all steps provided *after* attending the Hands-on lab.
