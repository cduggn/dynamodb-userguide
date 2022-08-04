## Working with DynamoDB

A guide to working with DynamoDB

## Selecting a Primary Key

- The primary key can be formualated with a single partition key or optionally as a composite key by including a sort key.
- The more unique the partition key the more uniformly data will be distributed across partitions.
- Primary keys comprising of a single partition key limits the data retrieval options and may require more scan operations to retrieve data.
- Including a sort key in the primary key can assist with not only sorting but also data retrieval by providing additional search capabilities.
- DynamoDB uses a consistent hashing scheme

## Querying

Use query operations over scan.

- Scan grabs everything in the table returning a collection of items and attributes.
- Queries use sort and partition keys to locate data directly.
- It has O(n) performance as it needs to iterate the entire data set.
- Scan is charged based on the amount of data scanned and not on what data is returned.
- Paralled scans are possible to increase the scan speed of retrieval however they can drain your read capacity quickly.
- Local secondary indexes rely on the same partition key as the primary index but allow you to choose a different sort key.
- Global secondary indexes allow for the creation of a completely new composite key index.
- The smaller your requests are the less you are charged. Keep records small to consume minimal units needed. Choose small attribute values and names.

## Scaling

- DynamoDB has extreme scaling handling millions of queries per second.
- Supports tables of any size.
- Updates are instant
