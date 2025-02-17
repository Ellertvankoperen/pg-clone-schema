# clone_schema

**clone_schema** is a PostgreSQL utility that makes a copy of a given schema (DDL and/or DATA). It works on most Linux distros and Windows versions.  It also runs on PostgreSQL in the cloud (AWS, GCP, MS Azure).

Handles following objects:

* Tables - structure (UDT columns, indexes, constraints, keys) and optionally, data
* Views, Materialized Views - Structure and data
* Sequences, Serial, Identity
* Functions/Procedures
* Types (composite and enum)
* Collations, Domains, Rules, Policies
* Triggers, Trigger Functions
* Comments
* ACLs (Permissions/Grants)

<br/>

Arguments:
* source schema
* target schema
* Enumerated list

<pre>source schema  Required: text - schema name</pre>
<pre>target schema  Required: text - table name</pre>
<pre>ENUM list      Required: One of 'DATA','NODATA','DDLONLY'</pre>
<pre>ENUM list      Optional: 'NOOWNER','NOACL','VERBOSE','FILECOPY'</pre>
By default, ownership and privileges are also cloned from source to target schema.  To override, specify **NOOWNER** and/or **NOACL** (similar to how pg_dump works). When **NOOWNER** is specified, the one running the script is the default owner unless overridden by a **SET ROLE** command before running this script. You may get faster results copying data to/from disk instead of in-memory copy. **FILECOPY** is a workaround for tables with complex UDT-type columns that fail to copy.  It only works for On-Prem PG Instances since it relies on using the COPY command to write to and read from disk.
<br/><br/>

Clone the schema with no data:
<br/>
>select clone_schema('sample', 'sample_clone', 'NODATA');
<br/>

Clone the schema with data:
<br/>
>select clone_schema('sample', 'sample_clone', 'DATA');<br/>
>select clone_schema('sample', 'sample_clone', 'DATA','VERBOSE');  -- show row copy progress
<br/>

Just generate DDL:
<br/>
>select clone_schema('sample', 'sample_clone', 'DDLONLY');

In this case, standard output with "INFO" lines are the generated DDL.
<br/><br/><br/>
The **schema_object_counts.sql** file is useful for validating the cloning results.  Just run it against source and target schemas to validate object counts after changing default schema name, **sample**.
<br/><br/>

# Regression Testing Overview
Regression Testing is done in the following order:
* Execute the **sampledb.sql** script to create the **clone_testing** database and the **sample** schema within it as the basis for the source schema.
* Clone the **sample** schema in the 3 ways possible (NODATA, DATA, DDLONLY).
* Run the **schema_object_counts.sql** queries to compare object counts and rows from the source and target schemas.
* Repeat all of the above for all supported versions of PG.

# Assumptions
* The target schema uses the same tablespace(s) as the source schema.

# Limitations
* Only works for PG Versions 10 and up.
* You should not clone the "public" schema.  The resulting output may not be accurate even if it finishes without any errors.
* Foreign Tables are not handled at the present time.  They must be done manually.
<br/>
<br/>
Sponsor:
 http://elephas.io/
<br/>
<br/> 
Compare cloning with EnterpriseDB's version that only works with their Advanced Server:
https://www.enterprisedb.com/edb-docs/d/edb-postgres-advanced-server/user-guides/user-guide/11/EDB_Postgres_Advanced_Server_Guide.1.078.html

 
 
 
