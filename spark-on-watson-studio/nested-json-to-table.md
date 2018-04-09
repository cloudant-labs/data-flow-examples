# Extract and save a nested JSON element into a separate Db2 table
This example extracts nested elements from a JSON array and saves each array item into new Db2 tables.  

**Note:** this is an introductory example and does not include any Db2 primary key-foreign key table relationships.

The example below will use this JSON document:
```json
{
  "_id": "90174e7b66274b00222ef6d8e60807f4",
  "array_of_objs": [
    {
      "number": 100,
      "boolean": true,
      "nestedKey": "value"
    },
    {
      "number": 200,
      "boolean": false,
      "nestedKey": "nextValue"
    }
  ]
}
```

## Python example
```python
# Import and initialize the SparkSession
from pyspark.sql import SparkSession
spark = SparkSession.builder.getOrCreate()

# Load Cloudant documents into a Spark DataFrame from the specified account
cloudantdata = spark.read.format("org.apache.bahir.cloudant")\
.option("cloudant.host", "account.cloudant.com")\
.option("cloudant.username", "username")\
.option("cloudant.password", "password")\
.load("database")

# Calculate the number of elements in the 'array_of_objs' column
nElements = len(cloudantdata.select('array_of_objs').collect()[0].array_of_objs)

# Create a new DataFrame for 'array_of_objs'
array_of_objs = cloudantdata.select('array_of_objs')

# Create properties for saving the DataFrame to a Db2 table
properties = {
    'user': 'username',
    'password': 'password',
    'driver': 'com.ibm.db2.jcc.DB2Driver'
}
db2_jdbc_url = 'jdbc:db2://****:50000/BLUDB'

# Create a new Spark DataFrame for each nested object in the 'array_of_objs' array
for i in range(nElements):
    # New dataframe for i item in 'array_of_objs' array
    new_table = array_of_objs.withColumn('array_of_objs-' + str(i), cloudantdata.array_of_objs.getItem(i))
    # Extract each nested object into their own column
    new_table = new_table.withColumn('array_of_objs-' + str(i) + '.number', new_table['array_of_objs-' + str(i)].number)\
    .withColumn('array_of_objs-' + str(i) + '.boolean', new_table['array_of_objs-' + str(i)].boolean)\
    .withColumn('array_of_objs-' + str(i) + '.nestedKey', new_table['array_of_objs-' + str(i)].nestedKey)    
    # Drop the 'array_of_objs' and 'array_of_objs-i' columns now that we have extracted all elements
    new_table = new_table.drop('array_of_objs').drop('array_of_objs-' + str(i))
    # Save the DataFrame to a Db2 Warehouse 'array_of_objsi' table
    new_table.write.jdbc(db2_jdbc_url, 'array_of_objs' + str(i), properties=properties)
```
