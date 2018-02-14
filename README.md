# data-flow-examples

A series of data flow example tutorials using various technologies to extract
data from IBM Cloudant and write the data elsewhere.

## Examples to IBM Db2 Warehouse on Cloud

These examples use Db2 Warehouse on Cloud as a destination.

**N.B. None of these examples are a direct replacement for the deprecated
Cloudant warehouse integration.**

The examples can be used as a starting point to develop application specific
data flows, but will need to be modified to meet your application requirements.
For example care should be taken around handling:
* document updates
* document deletions
* schema modifications/table alteration
* nested JSON structures and other data type limitations
