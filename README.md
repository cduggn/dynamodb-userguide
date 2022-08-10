## Working with DynamoDB

A guide to working with DynamoDB

## Selecting a Primary Key

- The primary key can be formulated with a single partition key or optionally as a composite key by including a sort key.
- The more unique the partition key the more uniformly data will be distributed across partitions.
- Primary keys comprising of a single partition key limits the data retrieval options and may require more scan operations to retrieve data.
- Including a sort key in the primary key can assist with not only sorting but also data retrieval by providing additional search capabilities.
- DynamoDB uses a consistent hashing scheme

## Querying

Use query operations over scan.

- Scan grabs everything in the table returning a collection of items and attributes.
- Queries use sort and partition keys to locate data directly.
- Scan has O(n) performance as it needs to iterate the entire data set.
- Scan is charged based on the amount of data scanned and not on what data is returned.
- Parallel scans are possible to increase the speed of retrieval however this can drain read capacity quickly.
- Local secondary indexes rely on the same partition key as the primary index but allow you to choose a different sort key.
- Global secondary indexes allow for the creation of a completely new composite key index.
- The smaller your requests are the less you are charged. Keep records small to consume minimal units needed. Choose small attribute values and names.

## Scaling

- DynamoDB has extreme scaling handling millions of queries per second.
- Supports tables of any size.
- Updates are instant

## PutItem

- By default PutItem returns no data.
- Use the ReturnValues parameter on input to instruct DynamoDB to return attributes. (options include ALL_OLD, ALL_NEW etc)
- Use conditional expressions to identify if a partition key already exists in the table thus preventing unexpected overwrites.
- There is no additional cost assoicated with requesting return value from PutItem operation. Aside from the processing overhead of receiving a larger response. No reach capacity units are consumed.
- PutItem only recognizes NONE or ALL_OLD values for ReturnValues attribute.
- 1 WCU per 1024bytes (1kb). Items can be up to 400kb creating a range between 1 and 400 WCU.
- Item size is sum of all its attributes sizes (name and value) and including hash and range keys.
- String and StringSet unicode with UTF-8 binary encoding. Consume between 1 and 4 bytes.
- 1 byte = English alphabet,numbers, punctuation , common symbols
- 2 bytes = pound sign, some languages like German
- 3 bytes = See docs , example Japenese language characters
- 4 bytes - emojis
- StringSet is a collection of strings - sum sizes of each string in the set

| Partition key | Sort Key | Attribute1|
| UUID | COUNTRY-REG-CITY| Name |
UUID - 4 BYTES , COUNTRY - 7 bytes, - 1 byte, REG - 3 bytes, - 1 byte, CITY - 4 bytes, ADDR - 4 bytes
TOTAL 24 BYTES per header

Sample Row
| 93f2528d-0a9d-4b3d-822b-b1c93d05db57 | Ireland#Connaught#Galway | Joe Bloggs |
37 bytes | 24 bytes | 10 bytes

## Choosing a UUID

- Use a UUID generator package based on [RFC4122](https://www.rfc-editor.org/rfc/rfc4122.html). No centralized authority is required to administer. The UUID's described in this specification are 128 bits long and can guarantee uniqueness across space and time. A Go implementation available from https://github.com/google/uuid.
- UUID generaters are less error prone then a solution using DynamoDB tables which need to coordinate the primary key counter using atomic ADD operations.
