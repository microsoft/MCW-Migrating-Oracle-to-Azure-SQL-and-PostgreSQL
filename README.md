# Retirement notice
This workshop will be retired effective August 30, 2022. At that time the repository will be archived, and the materials will not be maintained.

For questions or concerns regarding the MCW library, email us at [AskMCW](mailto:AskMCW@microsoft.com). Please remember to include the title of the workshop in your email.

# Migrating Oracle to Azure SQL and PostgreSQL

Wide World Importers (WWI) has experienced significant growth in the last few years. As a result, the Oracle OLTP database that powers their vendor integrations and inventory and sales applications has proved inadequate. The CIO has read about the performance and security enhancements of Azure SQL Database. Before committing to an Azure migration, the CIO would like a PoC of the OLTP database migration and the necessary modifications to the ASP.NET Core inventory app.

WWI has also learned of the benefits that Azure Database for PostgreSQL offers for an Oracle migration, including the similarities between PL/SQL and PL/pgSQL. Consult the [PostgreSQL Migration Lab document](Hands-on%20lab/HOL%20step-by-step%20-%20Migrating%20Oracle%20to%20PostgreSQL.md) to use the open-source `ora2pg` tool to assess an Oracle transactional database and migrate it to Azure.

In addition to predictable growth, they’ve had a substantial amount of growth in the data they store in their data warehouse. Their data warehouse is starting to show its age, slowing down during extract, transform, and load (ETL) operations and during critical queries. The data warehouse is running on SQL Server 2008 R2 Standard Edition. The CIO is excited about the potential performance improvements offered by Azure SQL Database, such as clustered ColumnStore indexes. She is also hoping that table compression can improve performance and backup times.

February 2022

## Target audience

- Application developer
- SQL Developer
- Database Administrator

## Abstracts

### Workshop

In this workshop, you gain a better understanding of how to conduct a site analysis for a customer to compare cost, performance, and level of effort required to migrate from Oracle to Azure SQL Database or Azure Database for PostgreSQL. There are two migration paths included in this workshop and the training files are named appropriately for each path. Both workshops share a common customer Oracle migration scenario. Each workshop path has tailored database platform migration steps to assist you in this learning journey.  You will evaluate the dependent applications and reports that need to be updated and come up with a migration plan. Also, you will design and build a proof of concept (POC) to help the customer take advantage of new Azure SQL Database or Azure Database for PostgreSQL features to improve performance and resiliency.

For those students focusing on the optional SQL Server migration (part of the [Oracle to Azure SQL Lab document](Hands-on%20lab/HOL%20step-by-step%20-%20Migrating%20Oracle%20to%20Azure%20SQL.md)), you will explore ways to migrate from an old version of SQL Server to the latest version (Azure SQL Database) and consider the impact of migrating from on-premises to the cloud.

Given the time required to complete the workshop, it is recommended that the student and trainer pick a single migration path.

### Whiteboard design session

In this whiteboard design session, you work with a group to design a proof of concept (POC) for conducting a site analysis for a customer to compare cost, performance, and level of effort required to migrate from Oracle to Azure SQL Database or Azure Database for PostgreSQL. You evaluate the dependent applications and reports that need to be updated and come up with a migration plan. Also, you review ways to help the customer take advantage of the database features to improve performance and resiliency. For the optional SQL Server path, you explore ways to migrate from an old version of SQL Server to the latest version (Azure SQL Database) and consider the impact of migrating from on-premises to the cloud.

At the end of this whiteboard design session, you will be better able to design a database migration plan and execute the steps.

### Hands-on lab

In this hands-on lab, you implement a proof of concept (POC) for conducting a site analysis for a customer to compare cost, performance, and migration level of effort. You will evaluate the dependent applications and reports that need to be updated and come up with a migration plan. Also, you help the customer take advantage of new features to improve performance and resiliency and perform a migration.

At the end of this hands-on lab, you will be better able to design and build a database migration plan and implement any required application changes associated with changing database technologies.

## Azure services and related products

- Azure App Services
- Azure Database Migration Service (DMS)
- Azure SQL Database
- Azure SQL Data Warehouse
- SQL Server on Azure Virtual Machines (SQL Server 2008 R2)
- Data Migration Assistant (DMA)
- SQL Server Management Studio (SSMS)
- SQL Server Migration Assistant (SSMA)
- Visual Studio 2019
- Azure Database for PostgreSQL
- ora2pg
- pgAdmin

## Azure solution

Data Modernization to Azure

## Related references

[MCW](https://microsoftcloudworkshop.com/)

## Help & Support

We welcome feedback and comments from Microsoft SMEs & learning partners who deliver MCWs.

**_Having trouble?_**

- First, verify you have followed all written lab instructions (including the Before the Hands-on lab document).
- Next, submit an issue with a detailed description of the problem.
- Do not submit pull requests. Our content authors will make all changes and submit pull requests for approval.

If you are planning to present a workshop, _review and test the materials early_! We recommend at least two weeks prior.

**Please allow 5 - 10 business days for review and resolution of issues.**
