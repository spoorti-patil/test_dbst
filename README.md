# External Data Package

The Coalesce External Data Package includes:

* [Inferschema](#inferschema)
* [CopyInto](#CopyInto)
* [Snowpipe](#Snowpipe)
* [External Table](#external-tables)
* [CopyUnload](#CopyUnload)
* [API](#API)
* [JDBC LOAD](#jdbc-load)
* [Parse Excel](#parse-excel)
* [Parse Json](#parse-json)
* [Code](#code)

<h2 id="inferschema"> InferSchema </h2>

The Coalesce InferSchema UDN is a versatile node infers schema of the file in internal or external stage and dynamically creates the target table of the same name as inferschema node.

[InferSchema](https://docs.snowflake.com/en/sql-reference/functions/infer_schema) in Snowflake automatically detects the file metadata schema in a set of staged data files that contain semi-structured data and retrieves the column definitions.

### InferSchema Prerequisites

* A sample file in the internal or external stage.
* An existing fileformat to parse the file
* The file format is also expected to be in the same location as stage

### InferSchema Node Configuration

The InferSchema node type has two configuration groups:

* [InferSchema Node Properties](#inferschema-node-properties)
* [InferSchema Source Data](#inferschema-source-data)

Go to the node and select the **Config tab** to see the Node Properties, Dynamic Table Options and General Options.

<h4 id="inferschema-node-properties"> InferSchema Node Properties </h4>

* **Storage Location**: Storage Location where the WORK will be created.
* **Node Type**: Name of template used to create node objects.
* **Description**: A description of the node's purpose.
* **Deploy Enabled**:
  * If TRUE the node will be deployed / redeployed when changes are detected.
  * If FALSE the node will not be deployed or will be dropped during redeployment.
  
<h4 id="inferschema-source-data"> InferSchema Source Data </h4>


* **Stage Storage Location (Required)**: A storage location in Coalesce where the stage is located.
* **Stage Name (Required)***: Internal or External stage where the files containing data to be loaded are staged.
* **File Names (Required)**: Use commas to seperate multiple files. File whose metatdata is to be inferred.
* **File Format Name (Required)***: Name of the file format object that describes the data contained in the staged files.It is expected in the same location as Stage.
* **Redeployment Behavior**:
  * **CREATE OR REPLACE**: Dynamically creates target table based on the inferred schema from file in staging area.
  * **ALTER EXISTING TABLE**: Dynamically alters inferred table by comparing the inferred schema of the same file (with changes if any)and created table.
  * **DROP EXISTING TABLE**: Drops the table inferred.

### InferSchema Example workflow

* Add a InferSchema node, for example `INFER_JSON`.
* Deploy the node. On deployment a table of the same name as the InferSchema node is created with columns inferred on parsing the file. For example, `InferSchema node:INFER_JSON,Inferred table:INFER_JSON`.
* Add the inferred table to the browser using Add Sources tab.
* Now we can add a Copy-Into Snowpipe or External table node on top of the inferred table to load staged files.
* If the structure of the file is changed, you can redeploy the InferSchema node with "Alter existing table" redeployment behaviour. The already inferred table is altered.
* Next, you can re-sync the columns of the inferred table and redeploy the CopyInto Copy-Into Snowpipe or External table node added on top of it.

> 🚧 Inferred table and Copy-into nodes/External table cannot be deployed together
>
> Infer Schema node, inferred table and Copy-into nodes/External table cannot be deployed together. First deploy the Infer Schema node. Then add the inferred table in browser, add Copy-into node on top of the table and then deploy the same.

### InferSchema Deployment

#### InferSchema Initial Deployment

When deployed for the first time into an environment the InferSchema node will execute the stage:

* Stage executed: **Infer and Create target table**

#### InferSchema Redeployment

**Redeployment Behavior: Create or Replace**

| Redeployment Behavior | Stage Executed |
|---|---|
| Create or Replace | Infer and Create target table

If any of the Source Data options like Stage storage location, Stage name or filename are modified.
Then you can redeploy the Infer Schema node with redeployment behaviour “Create or Replace”.

> 📘 Info
