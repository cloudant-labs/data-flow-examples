# IBM Cloudant to Db2 tutorials using Apache Spark™ in Watson Studio

**Note:** These examples are only intended as a starting point for loading Cloudant documents into Apache Spark and inserting them into Db2 Warehouse on Cloud. They are not intended as a drop-in replacement for the deprecated Cloudant warehouse integration. In particular these examples do not perform upsert or handle document deletion.

This directory contains the following example tutorials:

- [Scala tutorial for saving 'animaldb' database to Db2 Warehouse on Cloud](animaldb-scala-load-to-dashdb.md)  
An introductory tutorial in Scala for loading Cloudant documents into a Db2 table.
  
- [Python tutorial for saving 'animaldb' database to Db2 Warehouse on Cloud](animaldb-python-load-to-dashdb.md)    
An introductory tutorial in Python for loading Cloudant documents into a Db2 table.

- [Python tutorial for saving filtered 'crimes' Spark DataFrame to Db2 Warehouse on Cloud](crimes-load-to-dashdb-python.md)    
A tutorial in Python for loading Cloudant documents into Apache Spark, and saving a nested JSON object from the documents into a Db2 table.
  
- [Scala tutorial for loading docs and saving filtered 'sales' Spark DataFrame to Db2 on Cloud](sales-continuous-load-cloudant-to-db2.md)    
A tutorial in Scala that leverages Spark Streaming to continuously load Cloudant documents into a Db2 table.

- [Scala and Python code sample for transforming JSON array/object to Db2 data types](json-array-object-to-db2-data-type.md)

- [Python code sample for saving a nested JSON element into a separate Db2 table](nested-json-to-table.md)
