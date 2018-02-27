# Transform JSON array or object into separate Db2 column data types

The Python and Scala code below will demonstrate how to unpack a JSON array or object into
separate Db2 columns.

Both examples below will use this JSON document example:
```
{
  "_id": "90174e7b66274b00222ef6d8e60807f4",
  "_rev": "1-ff6661fc0e8d8496a1ad4868407ed91e",
  "array": [
    "Aberystwyth",
    "Dorchester",
    9
  ],
  "object": {
    "number": 100,
    "boolean": true,
    "nestedKey": "value"
  }
}
```

**Note: Extracting JSON object and arrays will create additional columns to the Db2 table.  Please be aware that the maximum column limit is 1012.  For more information, see the 
[CREATE TABLE statement documentation.](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.sql.ref.doc/doc/r0000927.html)**

## Python examples

### Extracting array elements
After connecting to Cloudant and loading documents into a Spark DataFrame (e.g. `cloudantdata`), we'll extract the elements from the `array` column:
```python
from pyspark.sql import functions as F            

# Calculate the number of elements in the 'array' column
nElements = cloudantdata.select("array").rdd.map(lambda x: len(x[0])).collect()[0]

# Extract and create a new column for each element in 'array' column
for i in range(nElements):
    cloudantdata = cloudantdata.withColumn("array." + str(i), F.col('array').getItem(i))

# Drop the 'array' column now that we have extracted all elements
cloudantdata.drop('array')
```

Table output from `cloudantdata.show()`:
```
+--------------------+--------------------+----------------+-----------+----------+-------+
|                 _id|                _rev|          object|    array.0|   array.1|array.2|
+--------------------+--------------------+----------------+-----------+----------+-------+
|90174e7b66274b002...|1-ff6661fc0e8d849...|[true,value,100]|Aberystwyth|Dorchester|      9|
+--------------------+--------------------+----------------+-----------+----------+-------+
```

### Extracting nested object properties
The code block below extracts each key/value pair in the `object` column:
```python
# Extract and create each key/value pair in the 'object' column into it's own new column
cloudantdata = cloudantdata.withColumn('object.number', cloudantdata.object.number)\
    .withColumn('object.nestedKey', cloudantdata.object.nestedKey)\
    .withColumn('object.boolean', cloudantdata.object.boolean)
    
# Drop the 'object' column now that we have extracted all elements
cloudantdata = cloudantdata.drop('object')
```

Table output from `cloudantdata.show()`:
```
+--------------------+--------------------+-----------+----------+-------+-------------+----------------+--------------+
|                 _id|                _rev|    array.0|   array.1|array.2|object.number|object.nestedKey|object.boolean|
+--------------------+--------------------+-----------+----------+-------+-------------+----------------+--------------+
|90174e7b66274b002...|1-ff6661fc0e8d849...|Aberystwyth|Dorchester|      9|          100|           value|          true|
+--------------------+--------------------+-----------+----------+-------+-------------+----------------+--------------+
```

## Scala examples

### Extracting array elements
```scala
// Calculate the number of elements in the 'array' column
val elementsOfArray = cloudantdata.select("array").collectAsList().get(0).getList(0)
val nElements = elementsOfArray.size()

// Extract and create a new column for each element in 'array' column
for (i <- 0 until nElements) {
    cloudantdata = cloudantdata.withColumn("array." + String.valueOf(i),
    cloudantdata.col("array").getItem(i))
}

// Drop 'array' column now that we have extracted all elements
cloudantdata = cloudantdata.drop("array")
```

Table output from `cloudantdata.show()`:
```
+--------------------+--------------------+----------------+-----------+----------+-------+
|                 _id|                _rev|          object|    array.0|   array.1|array.2|
+--------------------+--------------------+----------------+-----------+----------+-------+
|90174e7b66274b002...|1-ff6661fc0e8d849...|[true,value,100]|Aberystwyth|Dorchester|      9|
+--------------------+--------------------+----------------+-----------+----------+-------+
```

### Extracting nested object properties
```scala
// Extract and create each key/value pair in the 'object' column into it's own new column
cloudantdata = cloudantdata.withColumn("object.number", cloudantdata.col("object.number")).
withColumn("object.nestedKey", cloudantdata.col("object.nestedKey")).
withColumn("object.boolean", cloudantdata.col("object.boolean"))

// Drop the 'object' column now that we have extracted all items
cloudantdata = cloudantdata.drop("object")
```

Table output from `cloudantdata.show()`:
```
+--------------------+--------------------+-----------+----------+-------+-------------+----------------+--------------+
|                 _id|                _rev|    array.0|   array.1|array.2|object.number|object.nestedKey|object.boolean|
+--------------------+--------------------+-----------+----------+-------+-------------+----------------+--------------+
|90174e7b66274b002...|1-ff6661fc0e8d849...|Aberystwyth|Dorchester|      9|          100|           value|          true|
+--------------------+--------------------+-----------+----------+-------+-------------+----------------+--------------+
```
