![Microsoft Cloud Workshops](https://github.com/Microsoft/MCW-Template-Cloud-Workshop/raw/main/Media/ms-cloud-workshop.png "Microsoft Cloud Workshops")

<div class="MCWHeader1">
Migrating Oracle to Azure SQL
</div>

<div class="MCWHeader2">
Whiteboard design session student guide
</div>

<div class="MCWHeader3">
February 2022
</div>

Information in this document, including URL and other Internet Web site references, is subject to change without notice. Unless otherwise noted, the example companies, organizations, products, domain names, e-mail addresses, logos, people, places, and events depicted herein are fictitious, and no association with any real company, organization, product, domain name, e-mail address, logo, person, place or event is intended or should be inferred. Complying with all applicable copyright laws is the responsibility of the user. Without limiting the rights under copyright, no part of this document may be reproduced, stored in or introduced into a retrieval system, or transmitted in any form or by any means (electronic, mechanical, photocopying, recording, or otherwise), or for any purpose, without the express written permission of Microsoft Corporation.

Microsoft may have patents, patent applications, trademarks, copyrights, or other intellectual property rights covering subject matter in this document. Except as expressly provided in any written license agreement from Microsoft, the furnishing of this document does not give you any license to these patents, trademarks, copyrights, or other intellectual property.

The names of manufacturers, products, or URLs are provided for informational purposes only and Microsoft makes no representations and warranties, either expressed, implied, or statutory, regarding these manufacturers or the use of the products with any Microsoft technologies. The inclusion of a manufacturer or product does not imply endorsement of Microsoft of the manufacturer or product. Links may be provided to third party sites. Such sites are not under the control of Microsoft and Microsoft is not responsible for the contents of any linked site or any link contained in a linked site, or any changes or updates to such sites. Microsoft is not responsible for webcasting or any other form of transmission received from any linked site. Microsoft is providing these links to you only as a convenience, and the inclusion of any link does not imply endorsement of Microsoft of the site or the products contained therein.

© 2022 Microsoft Corporation. All rights reserved.

Microsoft and the trademarks listed at <https://www.microsoft.com/en-us/legal/intellectualproperty/Trademarks/Usage/General.aspx> are trademarks of the Microsoft group of companies. All other trademarks are property of their respective owners.

**Contents**

- [Migrating Oracle to Azure SQL whiteboard design session student guide](#migratingoracletoazuresql-whiteboard-design-session-student-guide)
  - [Abstract](#abstract)
  - [Step 1: Review the customer case study](#step-1-review-the-customer-case-study)
    - [Customer situation](#customer-situation)
    - [Customer needs](#customer-needs)
    - [Customer objections](#customer-objections)
    - [Infographic for common scenarios](#infographic-for-common-scenarios)
  - [Step 2: Design a proof of concept solution](#step-2-design-a-proof-of-concept-solution)
  - [Step 3: Present the solution](#step-3-present-the-solution)
  - [Wrap-up](#wrap-up)
  - [Additional references](#additional-references)

# Migrating Oracle to Azure SQL whiteboard design session student guide

## Abstract

In this whiteboard design session, you work with a group to design a proof of concept (POC) for conducting a site analysis for a customer to compare cost, performance, and level of effort required to migrate from Oracle to Azure SQL Database. You evaluate the dependent applications and reports that need to be updated and come up with a migration plan. Also, you review ways to help the customer take advantage of new Azure SQL Database features to improve performance and resiliency, as well as explore ways to migrate from an old version of SQL Server to the latest version and consider the impact of migrating from on-premises to the cloud.

At the end of this whiteboard design session, you will be better able to design a database migration plan and implementation.

## Step 1: Review the customer case study 

**Outcome**

Analyze your customer's needs.

Timeframe: 15 minutes

Directions: With all participants in the session, the facilitator/SME presents an overview of the customer case study along with technical tips.

1. Meet your team members and trainer.

2. Read all directions for steps 1-3 in the student guide.

3. As a team, review the following customer case study.

### Customer situation

Wide World Importers (WWI) has experienced massive growth over the last few years. That growth has resulted in a tremendous influx of new data they need to maintain their business. This data has become increasingly expensive to store in an Oracle relational database management system (RDBMS). Oracle upgrades are tedious and costly projects. Business stakeholders have tired of the process and have requested a proof of concept (POC) for replacing Oracle with Azure SQL Database.

WWI is investigating ways to improve the performance of their transactional databases without incurring expensive new license fees. They're also concerned with keeping their transactional system available and online for their store. They've noticed that Oracle has been slowing down as their growth has doubled. They realize that they would need to invest in new hardware to achieve this on-premises and, as a result, are looking at this as more of a migration to a new system.

WWI has several external and internal applications that need to migrate with the database. The database is used by an online store application, written in ASP.NET Core MVC. They also have internal applications that manage their product catalog, written in Oracle Forms. Also, they have many reports to aid in forecasting, sales reporting, and inventory maintenance. Those reports are a mixture of SQL Server Reporting Services (SSRS), Excel, and Oracle Forms and hit the Oracle OLTP database directly.

WWI also uses this database to interact with vendors. Several of their vendors require real-time access to their sales data through an API so they can draw warranty information on the date of sale. They do this through a Representational State Transfer (REST) service that is maintained by WWI.

WWI also loads the Oracle database into a Microsoft SQL Server 2008 R2 Standard Edition data warehouse. The head of IT consistently fails an audit because their data warehouse does not provide encryption of data at rest. He is also aware that the extended support period for SQL Server 2008 R2 has passed, which translates into more security, compliance, and audit implications. He realizes he needs to upgrade to Azure SQL Database or SQL Server Enterprise Edition and would like to include an upgrade in the POC.

Before WWI invests in this project, they want a proof of concept that encompasses these touchpoints and proves that it can be successful.

WWI also has a new requirement. They have an existing web service that interacts with a vendor to get the latest certifications of that vendor's products. The JSON parser sometimes fails, and they can't figure out why. They'd like to store the original, unparsed JSON in a table for troubleshooting purposes. They would like to be able to query the JSON data by date or other identifying pieces of the JSON that might be available for troubleshooting and are interested in learning more about the best way to do that.

Also, they had a significant outage last year because one of their audit tables ran out of space. They had to wait many hours to resolve the issue while their IT department scrambled to make space on an already overloaded Storage Area Network (SAN). They would like a full briefing on how to monitor that situation, so it doesn't happen again, and possible remedies if it does happen again. They would also like high availability to be built into the project plan and are wondering what additional fees that would incur.

Kathleen Sloan, the CIO of WWI, is looking to decrease their software license fees, take advantage of a modern data warehouse, and provide a strong vision of availability for the future that can handle their momentous growth. She is sold on SQL Server, and cloud Azure SQL Database, but her Oracle DBAs keep telling her that migration to SQL Server is simply impossible. They cite that they have never done it before. They say it's hard to find tools that make it possible. The Oracle DBAs say that SQL Server is not as high performing as Oracle, does not have great high availability like Oracle Real Application Clusters (RAC), and doesn't have a replacement for Oracle Forms. She's tired of hearing "no" whenever the topic is brought up and would love to prove them wrong. She is also exploring the cloud and is wondering if she can make a direct migration to a cloud database or if she must stay on-premises because of her requirements. She has seen SQL Server make tremendous gains in features over the last several versions, particularly in business intelligence. Her data warehouse has slowed down as the data has increased. On the one hand, this is because the data warehouse SQL Server has gotten very popular, but it's still considered a negative in her mind. She's concerned about the upgrade path because of all the dependencies on the data warehouse in her organization.

### Customer needs

1. Wants to migrate an existing Oracle database to SQL Server 2019 on-premises, SQL Server 2019 in an Azure VM, or Azure SQL Database.

2. Needs to know what's involved in migrating the external sales application to Azure SQL Database.

3. Wants a better understanding of what to do with the internal Oracle Forms application.

4. Has multiple touchpoints with external vendors and wants to know what needs to change with those web services.

5. Wants to upgrade their existing data warehouse from SQL Server 2008 R2 Standard Edition to Azure SQL Database or SQL Server 2019 Enterprise Edition to take advantage of some new features:

   - They want Transparent Data Encryption (TDE), so they pass audits when asked if they encrypt data at rest.
   - They want compression for some of their large fact tables.
   - They want to implement SSRS mobile reporting.
   - They heard about in-memory structures and are wondering if they can benefit from those. They aren't entirely sure how it is different from what they are using now.
   - They have a lot of SQL Server Integration Services (SSIS) packages that execute through SQL Server Agent jobs. They'd like to know the upgrade path for those.

6. Need web-based visualizations on sales and forecasting, and a plan on how to upgrade their existing reporting infrastructure.

7. Have a new requirement on what to do with JSON data.

8. They experienced an outage last year and are hyper concerned with not repeating that experience. The audit table filled up, and they ran out of disk space. They'd like to know what would have happened if SQL Server experienced the same issue and what your solution would be.

9. As a follow-up, they'd also like to know how to answer the Oracle DBA's allegation that SQL Server doesn't have an answer for Oracle RAC.

### Customer objections

1. Do we need to upgrade to on-premises SQL Server first or can we go straight to Azure?

2. Can we have two proof of concepts that demonstrate both migrations?

3. Do we need to rewrite all our applications for SQL Server or Azure SQL Database?

4. Do we need to rewrite all our reports for SQL Server or Azure SQL Database?

5. Will our security migrate over from Oracle? How do we handle security in the new database?

6. Do we need to invest in a JSON storage system for the JSON data we're storing from our vendor's web service?

7. What will we do if our audit logs fill up again? Will SQL Server crash the same way Oracle did?

8. If we take advantage of new features, will our license costs keep ratcheting up and up? Will we have a dependable way of budgeting for this project?

9. Are there any Oracle features required by WWI for which SQL Server or Azure offerings have no equivalent?

10. Do we need to tell all our vendors that we're changing databases, so their integrations work?

11. What might prevent us from upgrading our data warehouse to Azure SQL Database or SQL Server 2019 Enterprise?

12. When we upgrade the data warehouse, how will we keep all our connected dependencies updated?

13. What will happen with SSIS, SSRS, and SQL Server Analysis Services (SSAS)?

14. How will security and SQL Agent Jobs be migrated?

### Infographic for common scenarios

![This common scenario diagram includes the following elements: API App for vendor connections; Web App for Internet Sales Transactions; Oracle Forms App for inventory management; Oracle DB OLTP RAC Server; SSRS 2008 for Reporting of OLTP, Data Warehouse, and Cubes; SSIS 2008 for a Data Warehouse Load; Excel for reporting; SQL Server 2008 R2 Standard for a Data Warehouse; and SSAS2008 for a Data Warehouse. ](media/common-scenarios.png "Common Scenario diagram")

## Step 2: Design a proof of concept solution

**Outcome**

Design a solution and prepare to present the solution to the target customer audience in a 15-minute chalk-talk format.

Timeframe: 60 minutes

**Business needs**

Directions: With your team, answer the following questions and be prepared to present your solution to others:

1. Who will you present this solution to? Who is your target customer audience? Who are the decision makers?

2. What customer business needs do you need to address with your solution?

**Design**

Directions: With your team, respond to the following questions:

_High-level architecture_

1. Without getting into the details (they are addressed in the following sections), diagram your initial vision for handling the top-level requirements for data loading, data preparation, storage, high availability, application migration, and reporting. You will refine this diagram as you proceed.

2. What should be included in the POC?

3. How will SQL Server and Azure SQL Database save them on licensing costs?

_Schema and data movement_

1. How would you recommend that WWI move their data and schema into Azure SQL Database? What services would you suggest and what are the specific steps they would need to take to prepare the data, to transfer the data, and where would the loaded data land?

2. Update your diagram with the data loading process with the steps you identified.

_Application changes_

1. What product would you recommend to WWI to migrate their storefront MVC application to the new database?

2. How would you migrate the Oracle Forms applications? How would you define success? Are there any technologies the customer needs to know?

3. What will you do about the vendor touchpoints? How will you recommend they store the JSON data?

_Data warehouse and reporting_

1. How can they discover which reports and Excel spreadsheets hitting the Oracle database need to be upgraded? What's a proper upgrade path?

2. What must change about the way WWI loads its data warehouse?

3. What components do they need to use to upgrade the SQL Server 2008 R2 data warehouse to Azure SQL Database or SQL Server 2019 Enterprise?

4. Identify the significant milestones of delivering an upgrade to Azure SQL Database or SQL Server 2019 Enterprise.

5. Are there any tools or processes that would make this easier? How does Azure Database Migration Service (DMS) compare to other Microsoft database migration tools, such as Database Migration Assistant (DMA) or SQL Server Migration Assistant (SSMA)?

6. What are the steps required to use the Azure Database Migration Service to perform a database migration?

7. What are the post-upgrade steps we should consider in the POC? How would this address their concerns?

_High Availability and Audit Table_

1. If our solution were SQL Server, what could WWI have done with the audit table when it filled up?

2. What are the SQL Server options for high availability?

_Azure SQL Database POC_

1. Should they move to on-premises first?

2. Is there any benefit to going straight to Microsoft Azure? Does Azure SQL Database take care of all their requirements?

3. Are there any questions we need to answer before we can begin a POC directly to Microsoft Azure?

**Prepare**

Directions: As a team:

1. Identify any customer needs that are not addressed with the proposed solution.

2. Identify the benefits of your solution.

3. Determine how you will respond to the customer's objections.

Prepare a 15-minute chalk-talk style presentation to the customer.

## Step 3: Present the solution

**Outcome**

Present a solution to the target customer audience in a 15-minute chalk-talk format.

Timeframe: 30 minutes

**Presentation**

Directions:

1. Pair with another team.

2. One group is the Microsoft team, the other is the customer.

3. The Microsoft team presents their proposed solution to the customer.

4. The customer makes one of the objections from the list of objections.

5. The Microsoft team responds to the objection.

6. The customer team gives feedback to the Microsoft team.

7. Switch roles and repeat Steps 2-6.

##  Wrap-up 

Timeframe: 15 minutes

Directions: Reconvene with the larger group to hear the facilitator/SME share the preferred solution for the case study.

## Additional references

|                                                          |                                                                                                                               |
| -------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| **Description**                                          | **Links**                                                                                                                     |
| Oracle Database to SQL Server Migration Assistant        | <https://msdn.microsoft.com/library/hh313159(v=sql.110).aspx/>                                                                |
| Azure SQL Database features                              | <https://docs.microsoft.com/azure/sql-database/sql-database-features/> <https://blogs.msdn.microsoft.com/datamigration/dma/>  |
| SQL Server Stretch Database                              | <https://azure.microsoft.com/services/sql-server-stretch-database>                                                            |
| SQL Server JSON data                                     | <https://msdn.microsoft.com/library/dn921897.aspx>                                                                            |
| Older Oracle Forms Migration guide                       | <https://technet.microsoft.com/library/bb463141.aspx/> <https://www.microsoft.com/sql-server/sql-license-migration/>          |
| Oracle License Assistance Program & Deployment Subsidies | <https://www.microsoft.com/sql-server/sql-license-migration/>                                                                 |
| Data Migration Assistant blog                            | <https://blogs.msdn.microsoft.com/datamigration/dma/> <https://technet.microsoft.com/library/bb463141.aspx/>                  |
| Database Experimentation Assistant (DEA)                 | <https://blogs.msdn.microsoft.com/datamigration/2016/10/24/database-experimentation-%20assistant-v1-0-preview>                |
| SQL Server CLR strict security                           | <https://docs.microsoft.com/sql/database-engine/configure-windows/clr-strict-security>                                        |
| Azure Database Migration Service Overview                | <https://docs.microsoft.com/azure/dms/dms-overview>                                                                           |
| SQL Server database migration to Azure SQL Database      | <https://docs.microsoft.com/en-us/azure/sql-database/sql-database-cloud-migrate>                                              |
| Differentiating Microsoft's database migration tools     | <https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/> |
| Migrate your Java applications from Oracle to SQL Server with the help of the Data Access Migration Toolkit | <https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727>
