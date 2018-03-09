## Differences between Cloudant to Db2 data flow solutions

### Source options from Cloudant:

technology | available document extract endpoints | notes
--- | --- | --- 
Apache Bahir Spark `sql-cloudant` connector | `/{db}/_changes`, `/{db}/_all_docs`, `/{db}/_design/{design_doc}/_view/{view_name}`, `/{db}/_design/{design_doc}/_search/{search_index_name}`, `/{db}/_find` | -
IBM Cloud Functions | `/{db}/_changes`, `/{db}/_design/{design_doc}/_view/{view_name}`, `/{db}/_design/{design_doc}/_search/{search_index_name}`, `/{db}/_find` | Cloud Function trigger is only available for `_changes` endpoint.
Node-RED | `/{db}/_design/{design_doc}/_search/{search_index_name}` | -

### Sink options to Db2:

technology | available SQL operations for writing to Db2 | notes
--- | --- | ---
Apache Spark | `CREATE TABLE`, `INSERT` | Spark save modes change table creation behavior:<br/>`SaveMode.ErrorIfExists` - try to create the table and error if it exists.<br/>`SaveMode.Append` - try to create the table, insert rows into existing table if present.<br/>`SaveMode.Overwrite` - try to create the table, dropping it first if it already exists.<br/>`SaveMode.Ignore` - try to create the table, doing nothing if it already exists.
IBM Cloud Functions | any | SQL operations available via bindings in multiple languages.
Node-RED | `INSERT` | The IBM Bluemix starter app built-in packages do not support additional SQL operations, but additional nodes may be available in the npm package manager.

