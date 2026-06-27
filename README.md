# demo-day-2
Data-Integration-Pipelines-for-NYC-Payroll-Data-Analytics
Develop a Data Analytics platform on Azure Synapse Analytics to answer specific questions.

Project Overview

Project Introduction

The City of New York would like to develop a Data Analytics platform on Azure Synapse Analytics to accomplish two primary objectives:

Analyze how the City's financial resources are allocated and how much of the City's budget is being devoted to overtime.

Make the data available to the interested public to show how the City’s budget is being spent on salary and overtime pay for all municipal employees.

You have been hired as a Data Engineer to create high-quality data pipelines that are dynamic, can be automated, and monitored for efficient operation. The project team also includes the city’s quality assurance experts who will test the pipelines to find any errors and improve overall data quality.

The source data resides in Azure Data Lake and needs to be processed in a NYC data warehouse in Azure Synapse Analytics. The source datasets consist of CSV files with Employee master data and monthly payroll data entered by various City agencies.

Step 1: Prepare the Data Infrastructure
Architecture
CSV Files
     │
     ▼
Azure Data Lake Storage Gen2
     │
     ├──────────────┐
     │              │
Azure SQL DB   Azure Synapse
(Current Data) (Data Warehouse)
     ▲
     │
Azure Data Factory
Part 1 – Create Azure Data Lake Storage Gen2
Step 1

Login to Azure Portal

https://portal.azure.com

Step 2

Search

Storage Accounts

Click

+ Create
Step 3

Fill the details

Resource Group

Use the provided Resource Group

Storage Account Name

Example

adlsnycpayroll-sharanv

Choose

Region:
Same as Resource Group

Performance

Standard

Redundancy

LRS

Click

Next → Advanced
Step 4

Enable

Hierarchical namespace

✔ Enable

This converts it into Azure Data Lake Gen2.

Click

Review + Create

Then

Create

Wait until deployment finishes.

Part 2 – Create Container

Open your Storage Account

Go to

Data Storage
→ Containers

Click

+ Container

Name

nycpayrollcontainer

Public access

Private

Create.

Part 3 – Create Folders

Open the container.

Create these folders.

Click

+ Add Directory

Directory 1

dirpayrollfiles

Directory 2

dirhistoryfiles

Directory 3

dirstaging

Now you'll have

nycpayrollcontainer

│
├── dirpayrollfiles
├── dirhistoryfiles
└── dirstaging
Part 4 – Upload Files

Open

dirpayrollfiles

Upload

EmpMaster.csv

AgencyMaster.csv

TitleMaster.csv

nycpayroll_2021.csv

Open

dirhistoryfiles

Upload

nycpayroll_2020.csv

Done.

Part 5 – Create Azure Data Factory

Search

Data Factory

Click

Create

Resource Group

Same Resource Group

Name

Example

adf-nycpayroll

Region

Same region

Version

V2

Review

Create

Wait until deployment completes.

Part 6 – Create Azure SQL Database

Search

SQL Database

Click

Create

Fill details

Resource Group

Provided Resource Group

Database Name

db_nycpayroll

Server

Create New

Example

sqlserver-sharan

Admin Login

azureadmin

Password

Create your password

Region

Same region

Compute

Click

Configure Database

Choose

Basic

Apply

Click

Review + Create

Create

Part 7 – Configure Firewall

Open SQL Database

Go to

Networking

Click

Add Client IP

Also enable

✔ Allow Azure services and resources to access this server

Save.

This is very important.

Part 8 – Open Query Editor

Open

db_nycpayroll

Click

Query Editor

Login using

Server Admin
Password
Part 9 – Create Table

Paste

CREATE TABLE [dbo].[NYC_Payroll_Data]
(
    [FiscalYear] [int] NULL,
    [PayrollNumber] [int] NULL,
    [AgencyID] [varchar](10) NULL,
    [AgencyName] [varchar](50) NULL,
    [EmployeeID] [varchar](10) NULL,
    [LastName] [varchar](20) NULL,
    [FirstName] [varchar](20) NULL,
    [AgencyStartDate] [date] NULL,
    [WorkLocationBorough] [varchar](50) NULL,
    [TitleCode] [varchar](10) NULL,
    [TitleDescription] [varchar](100) NULL,
    [LeaveStatusasofJune30] [varchar](50) NULL,
    [BaseSalary] [float] NULL,
    [PayBasis] [varchar](50) NULL,
    [RegularHours] [float] NULL,
    [RegularGrossPaid] [float] NULL,
    [OTHours] [float] NULL,
    [TotalOTPaid] [float] NULL,
    [TotalOtherPay] [float] NULL
)
GO

Click

Run

Refresh

Tables

You should see

dbo.NYC_Payroll_Data
Part 10 – Create Synapse Workspace

Search

Azure Synapse Analytics

Click

Create

Fill

Workspace Name

Example

synapse-sharan

Resource Group

Same Resource Group

Managed Resource Group

Leave default.

Under Data Lake Storage Gen2, create a new storage account and file system (or use the options required by your lab instructions).

Click

Review + Create

Create

Deployment takes around

5–10 minutes
Part 11 – Open Synapse Studio

Open your workspace.

Click

Open Synapse Studio
Part 12 – Create Dedicated SQL Pool

Inside Synapse

Go to

Manage

↓

SQL Pools

↓

+ New

Name

SQLPool01

Performance

DW100c

Create

Wait

2–5 minutes
Part 13 – Open Develop

Go to

Develop

Click

+

↓

SQL Script
Part 14 – Select Dedicated SQL Pool

At the top of the SQL script window you'll see

Built-in

Change it to

SQLPool01

This is extremely important.

Do not run the scripts on the Built-in pool.

Part 15 – Create Four Tables

Paste all four SQL scripts provided by the project:

Employee Master (NYC_Payroll_EMP_MD)
Job Title (NYC_Payroll_TITLE_MD)
Agency Master (NYC_Payroll_AGENCY_MD)
Payroll Transaction (NYC_Payroll_Data)

Click

Run
Part 16 – Verify

Go to

Data

↓

Workspace

↓

SQL Database

↓

dbo

You should now see:

NYC_Payroll_EMP_MD

NYC_Payroll_TITLE_MD

NYC_Payroll_AGENCY_MD

NYC_Payroll_Data

Congratulations! Step 1 is complete.

Screenshots to Capture (Required for Submission)

Make sure you capture these screenshots as evidence:

Azure Data Lake Storage account with the three directories (dirpayrollfiles, dirhistoryfiles, dirstaging).
Uploaded CSV files in dirpayrollfiles.
nycpayroll_2020.csv uploaded in dirhistoryfiles.
Azure Data Factory resource overview.
Azure SQL Database (db_nycpayroll) showing the NYC_Payroll_Data table.
Synapse workspace overview.
Dedicated SQL pool (DW100c) details.
Synapse Studio showing the four created tables under the dbo schema.

After you finish this infrastructure setup, the next stage of the project is to build the Azure Data Factory pipelines, create linked services, datasets, copy activities, and mapping data flows to load and transform the payroll data.

This is the most important part of the project. You'll create 5 Data Flows and 5 Pipelines (or optionally 1 Master Pipeline). Follow these steps carefully.

Step 4: Create Data Flows
Overview

You will create these Data Flows:

Data Flow	Source	Destination
DF_2021_SQLDB	nycpayroll_2021.csv	SQL DB NYC_Payroll_Data
DF_EMP	EmpMaster.csv	Synapse NYC_Payroll_EMP_MD
DF_TITLE	TitleMaster.csv	Synapse NYC_Payroll_TITLE_MD
DF_AGENCY	AgencyMaster.csv	Synapse NYC_Payroll_AGENCY_MD
DF_PAYROLL_SYN	SQL DB NYC_Payroll_Data	Synapse NYC_Payroll_Data
Part 1 – Create Data Flow for 2021 Payroll → SQL DB
Step 1

Open Azure Data Factory Studio.

Go to:

Author (✏️)

Click:

+ → Data Flow

Choose:

Add Source
Step 2 – Configure Source

Source Name

Source_2021

Dataset

DS_nycpayroll_2021

Import Projection

Click

Import Projection

This reads the CSV schema.

Step 3 – Fix Data Types

Go to:

Projection

Change the data types to match the SQL table.

Column	Source Type	Change To
FiscalYear	String	Integer
PayrollNumber	String	Integer
AgencyStartDate	String	Date
BaseSalary	String	Double
RegularHours	String	Double
RegularGrossPaid	String	Double
OTHours	String	Double
TotalOTPaid	String	Double
TotalOtherPay	String	Double

Ensure AgencyStartDate is recognized as a Date.

Step 4 – Add Sink

Click the + below the Source and select:

Sink

Dataset

DS_SQL_NYC_Payroll
Step 5 – Configure Sink

In the Settings tab:

Enable Allow insert
Disable Auto Mapping

Go to the Mapping tab.

Click:

Import Schemas

Verify that each source column maps to the correct destination column.

Example:

Source	Sink
FiscalYear	FiscalYear
PayrollNumber	PayrollNumber
AgencyID	AgencyID
AgencyName	AgencyName
EmployeeID	EmployeeID
...	...

If any mapping is missing, add it manually.

Step 6 – Data Preview (Optional)

Turn on:

Data Flow Debug

Wait until the Spark cluster starts (may take 3–5 minutes).

Click:

Data Preview

Verify the data.

Step 7

Click:

Publish All
Part 2 – Create Pipeline for SQL DB

Go to:

Author

↓

+ → Pipeline

Name:

PL_Load_2021_SQLDB

From Activities:

Drag:

Data Flow

onto the canvas.

Select:

DF_2021_SQLDB

Publish.

Trigger

Click:

Add Trigger

↓

Trigger Now

Click

OK
Monitor

Go to:

Monitor

Wait until the pipeline status shows:

Succeeded
Verify SQL DB

Open:

Azure SQL Database

↓

Query Editor

Run:

SELECT COUNT(*) FROM NYC_Payroll_Data;

If rows are returned, the data loaded successfully.

Part 3 – Create Employee Data Flow

Create another Data Flow.

Source

DS_EmpMaster

Sink

DS_EMP_MD

Mapping

EmployeeID → EmployeeID

LastName → LastName

FirstName → FirstName

Publish.

Part 4 – Create Title Data Flow

Source

DS_TitleMaster

Sink

DS_TITLE_MD

Map:

TitleCode

↓

TitleCode
TitleDescription

↓

TitleDescription

Publish.

Part 5 – Create Agency Data Flow

Source

DS_AgencyMaster

Sink

DS_AGENCY_MD

Map:

AgencyID

↓

AgencyID
AgencyName

↓

AgencyName

Publish.

Part 6 – Create Payroll SQL DB → Synapse Data Flow

Create another Data Flow.

Source Dataset

DS_SQL_NYC_Payroll

Sink Dataset

DS_SYN_Payroll

Disable:

Auto Mapping

Import Schemas.

Verify all 19 columns are correctly mapped.

Publish.

Part 7 – Create Pipelines

Create these pipelines:

Pipeline	Data Flow
PL_EMP	DF_EMP
PL_TITLE	DF_TITLE
PL_AGENCY	DF_AGENCY
PL_PAYROLL_SYN	DF_PAYROLL_SYN

Each pipeline contains one Data Flow activity.

Configure Staging

For every Synapse sink:

Go to:

Sink

↓

Settings

Enable staging.

Choose:

LS_ADLS

Folder:

dirstaging

This is required because Synapse loads data through staged files.

Part 8 – (Optional) Create Master Pipeline

Instead of triggering four pipelines separately, you can create one pipeline.

Name:

PL_MASTER

Drag four Execute Pipeline activities.

Execute:

PL_EMP

↓

PL_TITLE

↓

PL_AGENCY

↓

PL_PAYROLL_SYN

Connect them sequentially or in parallel.

Publish.

Part 9 – Run Pipelines

Click:

Trigger Now

Monitor:

Monitor

↓

Pipeline Runs

Wait until every pipeline shows:

Succeeded
Verify Synapse Tables

Open Synapse Studio → Develop → SQL Script.

Run:

SELECT COUNT(*) FROM dbo.NYC_Payroll_EMP_MD;
SELECT COUNT(*) FROM dbo.NYC_Payroll_TITLE_MD;
SELECT COUNT(*) FROM dbo.NYC_Payroll_AGENCY_MD;
SELECT COUNT(*) FROM dbo.NYC_Payroll_Data;

Each query should return a row count greater than 0.

Screenshots Required for Submission

Capture these screenshots:

DF_2021_SQLDB showing the Source and Sink.
Mapping tab for the payroll Data Flow.
PL_Load_2021_SQLDB pipeline after a Succeeded run.
DF_EMP, DF_TITLE, and DF_AGENCY Data Flows.
DF_PAYROLL_SYN (SQL DB → Synapse).
PL_MASTER (or the four individual pipelines).
Monitor page showing successful pipeline runs.
Synapse query results showing that all four destination tables contain data.

Tip: If a pipeline fails, first check:

Source and sink mappings.
Data types (especially FiscalYear, PayrollNumber, and AgencyStartDate).
That Auto Mapping is disabled in the sink and all mappings are manually aligned.
That the Synapse sink uses the dirstaging folder for staging.
Step 5: Data Aggregation and Parameterization

In this step, you'll extract the 2021 year data and historical data, merge, aggregate and store it in Synapse Analytics. The aggregation will be on Agency Name, Fiscal Year and TotalPaid.

1. Create a Summary table in Synapse with the following SQL script and create a dataset named table_synapse_nycpayroll_summary

CREATE TABLE [dbo].[NYC_Payroll_Summary]( [FiscalYear] [int] NULL, [AgencyName] [varchar](50) NULL, [TotalPaid] [float] NULL )

2. Create a new dataset for the Azure Data Lake Gen2 folder that contains the historical files.

Select dirhistoryfiles in the data lake as the source

3.Create new data flow and name it Dataflow Aggregate Data

Create a data flow level parameter for Fiscal Year

Add first Source for table_sqldb_nyc_payroll_data table

Add second Source for the Azure Data Lake history folder

4.Create a new Union activity in the data flow and Union with history files

5.Add a Filter activity after Union

In Expression Builder, enter toInteger(FiscalYear) >= $dataflow_param_fiscalyear

6.Derive a new TotalPaid column

In Expression Builder, enter RegularGrossPaid + TotalOTPaid+TotalOtherPay



7.Add an Aggregate activity to the data flow next to the TotalPaid activity

Under Group By, Select AgencyName and Fiscal Year

8.Add a Sink activity to the Data Flow

Select the dataset to target (sink) the data into the Synapse Analytics Payroll Summary table.

In Settings, select Truncate Table





9.Create a new Pipeline and add the Aggregate data flow

Create a new Global Parameter (This will be the Parameter at the global pipeline level that will be passed on to the data flow

In Parameters, select Pipeline Expression

Choose the parameter created at the Pipeline level



10.Validate, Publish and Trigger the pipeline. Enter the desired value for the parameter.

11.Monitor the Pipeline run and take a screenshot of the finished pipeline run.

Excellent! Step 5 is the final and most advanced part of the project. Here you'll use Union, Filter, Derived Column, Aggregate, Parameters, and Pipeline Parameterization in Azure Data Factory.

Step 5: Data Aggregation and Parameterization
Objective

You will combine:

2021 Payroll Data (from Azure SQL Database)
2020 Historical Payroll Data (from Azure Data Lake)

Then:

Filter by Fiscal Year using a parameter
Calculate TotalPaid
Aggregate data by AgencyName and FiscalYear
Load the results into a summary table in Synapse
Data Flow Architecture
Azure SQL DB (2021)
          \
           \
            UNION
           /
ADLS History (2020)
        │
        ▼
      Filter
        │
        ▼
 Derived Column (TotalPaid)
        │
        ▼
     Aggregate
        │
        ▼
Synapse Summary Table
Part 1 – Create Summary Table in Synapse

Open Synapse Studio.

Go to:

Develop

Create a new SQL Script.

Select your Dedicated SQL Pool (not Built-in).

Run:

CREATE TABLE [dbo].[NYC_Payroll_Summary]
(
    [FiscalYear] INT NULL,
    [AgencyName] VARCHAR(50) NULL,
    [TotalPaid] FLOAT NULL
)
GO

Verify the table appears under:

Data
   ↓
Workspace
   ↓
Dedicated SQL Pool
   ↓
dbo
   ↓
NYC_Payroll_Summary
Part 2 – Create Synapse Dataset

Go to:

ADF → Author

↓

+ → Dataset

Choose

Azure Synapse Analytics

Dataset Name

DS_SYN_Payroll_Summary

Linked Service

LS_Synapse

Table

dbo.NYC_Payroll_Summary

Publish.

Part 3 – Create Dataset for Historical Folder

Create another Dataset.

Choose

Azure Data Lake Storage Gen2

↓

DelimitedText

Dataset Name

DS_History_Payroll

Linked Service

LS_ADLS

Browse to:

Container

↓

dirhistoryfiles

↓

nycpayroll_2020.csv

Enable

✔ First row as header

Preview Data.

Publish.

Part 4 – Create Data Flow

Go to

Author

↓

+ → Data Flow

Name it

DF_Aggregate_Data
Part 5 – Create Data Flow Parameter

Click on empty canvas.

Open:

Parameters

Click

+ New

Name

dataflow_param_fiscalyear

Type

Integer

Default Value (optional)

2020
Part 6 – Add First Source

Click

Add Source

Dataset

DS_SQL_NYC_Payroll

Name

Source_SQLDB

Import Projection.

Part 7 – Add Second Source

Add another Source.

Dataset

DS_History_Payroll

Name

Source_History

Import Projection.

If required, update the projection so data types match the SQL source:

FiscalYear → Integer
PayrollNumber → Integer
AgencyStartDate → Date
Numeric fields → Double
Part 8 – Add Union

Click + after the first source.

Select:

Union

Connect:

Source_SQLDB
Source_History

Union by Name.

Part 9 – Add Filter

After the Union, add a Filter transformation.

Expression:

toInteger(FiscalYear) >= $dataflow_param_fiscalyear

This means:

If parameter = 2021 → only 2021 data.
If parameter = 2020 → both 2020 and 2021 data.
Part 10 – Derived Column

Add a Derived Column transformation.

Create a new column:

Column Name

TotalPaid

Expression:

RegularGrossPaid + TotalOTPaid + TotalOtherPay
Part 11 – Aggregate

Add an Aggregate transformation.

Group By

Select:

AgencyName

and

FiscalYear
Aggregate Column

Create:

Column Name

TotalPaid

Expression:

sum(TotalPaid)

This produces one row per Agency and Fiscal Year.

Part 12 – Add Sink

Add a Sink.

Dataset

DS_SYN_Payroll_Summary
Sink Settings

Go to Settings.

Enable:

Insert

Check:

Truncate Table

This clears the table before loading fresh results.

If staging is required:

Linked Service: LS_ADLS
Folder:
dirstaging
Part 13 – Create Pipeline

Go to:

Author

↓

+ → Pipeline

Name:

PL_Aggregate_Data

Drag a Data Flow activity.

Select:

DF_Aggregate_Data
Part 14 – Create Pipeline Parameter

Click on the pipeline canvas.

Go to:

Parameters

Click:

+ New

Name:

FiscalYear

Type:

Integer

Default:

2020
Part 15 – Pass Parameter to Data Flow

Select the Data Flow activity.

Open the Parameters tab.

For:

dataflow_param_fiscalyear

Click Add Dynamic Content.

Choose:

Pipeline Expression

Select:

pipeline().parameters.FiscalYear

This passes the pipeline parameter into the data flow.

Part 16 – Validate and Publish

Click:

Validate

If validation succeeds:

Publish All

Wait for:

Publish Succeeded
Part 17 – Trigger Pipeline

Click:

Add Trigger

↓

Trigger Now

When prompted, enter the Fiscal Year.

Example:

2020

or

2021

Click OK.

Part 18 – Monitor

Go to:

Monitor

↓

Pipeline Runs

Wait until the status shows:

Succeeded
Part 19 – Verify the Summary Table

Open Synapse Studio.

Run:

SELECT *
FROM dbo.NYC_Payroll_Summary;

You should see output like:

FiscalYear	AgencyName	TotalPaid
2020	Police Department	12567890
2020	Fire Department	8945621
2021	Police Department	13145622
2021	Fire Department	9023487

(Your values will depend on the project data.)

Final Project Checklist

By the end of Step 5, your ADF project should contain:

Linked Services (3)
✅ LS_ADLS
✅ LS_SQLDB
✅ LS_Synapse
Datasets (11)
✅ 4 CSV datasets
✅ SQL DB dataset
✅ 4 Synapse table datasets
✅ Summary table dataset
✅ History CSV dataset
Data Flows (6)
✅ DF_2021_SQLDB
✅ DF_EMP
✅ DF_TITLE
✅ DF_AGENCY
✅ DF_PAYROLL_SYN
✅ DF_Aggregate_Data
Pipelines
✅ PL_Load_2021_SQLDB
✅ PL_EMP
✅ PL_TITLE
✅ PL_AGENCY
✅ PL_PAYROLL_SYN
✅ PL_Aggregate_Data
(Optional) ✅ PL_MASTER
Screenshots Required for Submission

Capture these before submitting:

NYC_Payroll_Summary table created in Synapse.
DF_Aggregate_Data showing the complete flow (Sources → Union → Filter → Derived Column → Aggregate → Sink).
Filter transformation with the parameter expression.
Derived Column transformation showing the TotalPaid expression.
Aggregate transformation showing Group By (AgencyName, FiscalYear) and sum(TotalPaid).
Pipeline parameter configuration and the Data Flow parameter mapping.
Monitor page showing the aggregate pipeline run as Succeeded.
Query results from SELECT * FROM dbo.NYC_Payroll_Summary; showing the aggregated output.

Completing this step means you've implemented a full Azure Data Factory ETL solution with parameterized pipelines, data transformation, aggregation, and loading into Azure Synapse Analytics, which covers the key concepts assessed in this project.

